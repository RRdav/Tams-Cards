<script lang="ts" setup>
    import { ref } from 'vue';
    import CardWrapper from '@/assets/Cards_wrapper.svg';
    import CardBack from '@/assets/Cards_back.svg';

    // Grab all Asset #.svg files in public folder
    const cardModules = import.meta.glob('@/assets/Asset [0-9]*.svg', { eager: true, import: 'default', query: '?url' });
    const cardUrls = Object.values(cardModules) as string[];

    // Grab 3 random unique cards from cardUrls
    const getRandomUniqueCards = (numCards: number): string[] => {
        const selectedCards = new Set<string>();
        while (selectedCards.size < numCards) {
            const randomIndex = Math.floor(Math.random() * cardUrls.length);
            const card = cardUrls[randomIndex];
            if (card) selectedCards.add(card);
        }
        return Array.from(selectedCards);
    }
    const selectedCardUrls = getRandomUniqueCards(3);

    // Track which cards are flipped
    const flippedCards = ref<boolean[]>(new Array(cardUrls.length).fill(false));
    // Track which cards are released
    const releasedCards = ref<boolean[]>(new Array(cardUrls.length).fill(false));
    // Track Animation state
    const isAnimating = ref(false);

    const isCardsReleased = ref(false);

    const flipCard = (index: number) => {
        flippedCards.value[index] = !flippedCards.value[index];
    }

    const releaseCardsAnimation = () => {
        // Set released state in Vue instead of DOM manipulation
        releasedCards.value = releasedCards.value.map(() => true);
        isCardsReleased.value = true;

        isAnimating.value = true;

        // After animation completes, disable animation flag
        setTimeout(() => {
            isAnimating.value = false;
        }, 1500);
    }

    const resetCards = () => {
        // Reset flipped and released states
        flippedCards.value = new Array(cardUrls.length).fill(false);
        releasedCards.value = new Array(cardUrls.length).fill(false);
        isCardsReleased.value = false;
        isAnimating.value = true;
        setTimeout(() => {
            selectedCardUrls.splice(0, selectedCardUrls.length, ...getRandomUniqueCards(3));
            isAnimating.value = false;
        }, 500);
    }
</script>

<template>
    <main>
        <div class="reset-cards-container">
            <button class="mtg-btn" v-if="isCardsReleased" @click="resetCards()">Reset Cards</button>
        </div>
        <div class="mtg-container">
            <img :src="CardWrapper" :class="['card-wrapper', isCardsReleased ? 'released' : '']" alt="Card Wrapper" @click="releaseCardsAnimation()"/>

            <!-- Loop through cards -->
            <div
                v-for="(cardUrl, index) in selectedCardUrls"
                :key="index"
                :class="[
                'card-container',
                { flipped: flippedCards[index] },
                { [`release-card-${index}`]: releasedCards[index] },
                { 'animating': isAnimating }
                ]"
                @click="flipCard(index)"
            >
                <div class="card-flipper">
                <img :src="CardBack" class="card card-back" :alt="`Card Back ${index + 1}`" />
                <img :src="cardUrl" class="card card-front" :alt="`Card ${index + 1}`" />
                </div>
            </div>
        </div>
    </main>
</template>

<style scoped>
main {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 100%;
}


.mtg-container {
    display: flex;
    justify-content: center;
    align-items: end;
    padding: 20px;
    width: 100%;
}

.card-wrapper {
    width: 150px;
    height: auto;
    cursor: pointer;
    position: relative;
    z-index: 1;
    top: 0;
    transition: top 1s ease;
}

.card-wrapper.released {
    top: 150px;
}

.card-container {
    width: 100px;
    height: 300px;
    perspective: 1000px;
    cursor: pointer;
    position: absolute;
    bottom: -5px;
    transition: transform 1s ease-in-out; /* Add this transition */
}

.card-flipper {
    position: relative;
    width: 100%;
    height: 100%;
    transition: transform 0.8s;
    transform-style: preserve-3d;
}

.card-container.flipped .card-flipper {
    transform: rotateY(180deg);
}

.card {
    width: 100%;
    height: 100%;
    position: absolute;
    backface-visibility: hidden;
}
.card-back {
    /* Visible by default */
}

.card-front {
    /* Hidden until flipped */
    transform: rotateY(180deg);
}

/* Only apply animation when animating class is present */
.card-container.release-card-0.animating {
    animation: releaseCard0 1.5s ease-in-out forwards;
}

.card-container.release-card-1.animating {
    animation: releaseCard1 1.5s ease-in-out forwards;
}

.card-container.release-card-2.animating {
    animation: releaseCard2 1.5s ease-in-out forwards;
}

/* Static positions after animation */
.card-container.release-card-0:not(.animating) {
    transform: translate(-150px, -200px) rotate(0deg) scale(1.2);
}

.card-container.release-card-1:not(.animating) {
    transform: translate(0px, -200px) rotate(0deg) scale(1.2);
}

.card-container.release-card-2:not(.animating) {
    transform: translate(150px, -200px) rotate(0deg) scale(1.2);
}


@keyframes releaseCard0 {
    0% { transform: translate(0, 0) rotate(0deg) scale(0.5) }
    100% { transform: translate(-150px, -200px) rotate(720deg) scale(1.2) }
}

@keyframes releaseCard1 {
    0% { transform: translate(0, 0) rotate(0deg) scale(0.5) }
    100% { transform: translate(0px, -200px) rotate(720deg) scale(1.2) }
}

@keyframes releaseCard2 {
    0% { transform: translate(0, 0) rotate(0deg) scale(0.5) }
    100% { transform: translate(150px, -200px) rotate(720deg) scale(1.2) }
}



</style>