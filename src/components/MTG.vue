<script lang="ts" setup>
    import { ref } from 'vue';
    import CardWrapper from '@/assets/Cards_wrapper.svg';
    import CardBack from '@/assets/Cards_back.svg';

    // Logic for card randomisation

    // Grab all Asset #.svg files in public folder
    const cardModules = import.meta.glob('@/assets/Card[0-9]*.svg', { eager: true, import: 'default', query: '?url' });
    const cardUrls = Object.values(cardModules) as string[];
    console.log('Loaded card URLs:', cardUrls);

    // Track which cards are flipped
    const flippedCards = ref<boolean[]>(new Array(cardUrls.length).fill(false));
    // Track which cards are released
    const releasedCards = ref<boolean[]>(new Array(cardUrls.length).fill(false));
    const isCardsReleased = ref(false);

    const flipCard = (index: number) => {
        flippedCards.value[index] = !flippedCards.value[index];
    }

    const releaseCardsAnimation = () => {
        // Set released state in Vue instead of DOM manipulation
        releasedCards.value = releasedCards.value.map(() => true);
        isCardsReleased.value = true;
    }

    const resetCards = () => {
        // Reset flipped and released states
        flippedCards.value = new Array(cardUrls.length).fill(false);
        releasedCards.value = new Array(cardUrls.length).fill(false);
        isCardsReleased.value = false;
    }
</script>

<template>
  <div class="mtg-container">
    <img :src="CardWrapper" class="card-wrapper" alt="Card Wrapper" @click="releaseCardsAnimation()"/>

    <!-- Loop through cards -->
    <div
      v-for="(cardUrl, index) in cardUrls"
      :key="index"
      :class="[
        'card-container',
        { flipped: flippedCards[index] },
        { [`release-card-${index}`]: releasedCards[index] }
      ]"
      @click="flipCard(index)"
    >
      <div class="card-flipper">
        <img :src="CardBack" class="card card-back" :alt="`Card Back ${index + 1}`" />
        <img :src="cardUrl" class="card card-front" :alt="`Card ${index + 1}`" />
      </div>
    </div>

    <!-- Reset Button (only show when cards are released) -->
    <button v-if="isCardsReleased" @click="resetCards()">Reset Cards</button>
  </div>
</template>

<style scoped>
.mtg-container {
    display: flex;
    justify-content: center;
    align-items: end;
    padding: 20px;
}

.card-wrapper {
    width: 250px;
    height: auto;
    cursor: pointer;
    position: relative;
    z-index: 1;
}

.card-container {
    width: 200px;
    height: 300px;
    perspective: 1000px;
    cursor: pointer;
    position: absolute;
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

.release-card-0 {
    animation: releaseCard0 1.5s ease-in-out forwards;
    transform: translate(-250px, -400px) rotate(0deg);
}

.release-card-1 {
    animation: releaseCard1 1.5s ease-in-out forwards;
    transform: translate(0px, -400px) rotate(0deg);
}

.release-card-2 {
    animation: releaseCard2 1.5s ease-in-out forwards;
    transform: translate(250px, -400px) rotate(0deg);
}


@keyframes releaseCard0 {
    0% { transform: translate(0, 0) rotate(0deg); }
    100% { transform: translate(-250px, -400px) rotate(720deg) }
}

@keyframes releaseCard1 {
    0% { transform: translate(0, 0) rotate(0deg); }
    100% { transform: translate(0px, -400px) rotate(720deg) }
}

@keyframes releaseCard2 {
    0% { transform: translate(0, 0) rotate(0deg); }
    100% { transform: translate(250px, -400px) rotate(720deg) }
}



</style>