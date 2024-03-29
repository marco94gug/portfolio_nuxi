<script setup lang="ts">
import { ref } from "vue";

const carousel = ref(null);
const clientWidth = ref(1);
const scrollLeft = ref(0);
const scrollWidth = ref(0);
const calculatedIndex = ref(0);

const { type } = defineProps<{
  length: number;
  type?: string;
}>();

onMounted(() => {
  const carouselRef = carousel.value! as HTMLDivElement;
  if ("onscrollend" in window) {
    carouselRef.addEventListener("scrollend", (e) => {
      updateScrollValue(e);
    });
  } else {
    carouselRef.onscroll = (event) => {
      clearTimeout((window as any).scrollEndTimer);
      (window as any).scrollEndTimer = setTimeout(
        () => updateScrollValue(event),
        100
      );
    };
  }
});

onBeforeRouteLeave(() => {
  const carouselRef = carousel.value! as HTMLDivElement;
  if ("onscrollend" in window) {
    carouselRef.removeEventListener("scrollend", (e) => {
      updateScrollValue(e);
    });
  }
});

const handleClick = (direction: "previous" | "next") => {
  const carouselEl = carousel.value! as HTMLDivElement;
  const isPrev = direction === "previous";
  const cardWidth = carouselEl.children[0].getBoundingClientRect().width ?? 0;

  const scrollLeft = carouselEl.scrollLeft + (isPrev ? -cardWidth : cardWidth);

  carouselEl.scroll({ left: scrollLeft, top: 0 });
};

const updateScrollValue = (e: Event) => {
  const targetScrollPosition = e.target as HTMLDivElement;
  const carouselEl = carousel.value! as HTMLDivElement;
  const cardWidth = carouselEl.children[0].getBoundingClientRect().width ?? 0;

  clientWidth.value = targetScrollPosition.clientWidth;
  scrollLeft.value = targetScrollPosition.scrollLeft;
  scrollWidth.value = targetScrollPosition.scrollWidth;
  calculatedIndex.value = Math.ceil(scrollLeft.value / cardWidth);
};

const isMaxScrollValue = computed((): boolean => {
  const scrollDimension = scrollWidth.value - clientWidth.value;

  if (scrollDimension > scrollLeft.value) {
    return scrollDimension === Math.ceil(scrollLeft.value);
  } else if (scrollDimension < scrollLeft.value) {
    return scrollDimension === Math.floor(scrollLeft.value);
  } else if (scrollDimension === scrollLeft.value) {
    return true;
  } else {
    return false;
  }
});

const isMinScrollValue = computed((): boolean => scrollLeft.value === 0);
</script>

<template>
  <div class="carousel">
    <div class="carousel__scroller" ref="carousel">
      <slot name="slides"></slot>
    </div>
    <div class="carousel__navigation-container">
      <button
        :class="`${isMinScrollValue ? 'inactive' : ''}`"
        @click="handleClick('previous')"
        aria-label="arrow-left"
      >
        <i class="pi pi-angle-left"></i>
      </button>
      <button
        :class="`${isMaxScrollValue ? 'inactive' : ''}`"
        @click="handleClick('next')"
        aria-label="arrow-right"
      >
        <i class="pi pi-angle-right"></i>
      </button>
    </div>
    <div class="carousel__pagination-container">
      <span
        v-for="(_, index) in Array(length)"
        :class="`${type ? 'photo' : ''} ${isIndexVisible(
          index,
          calculatedIndex
        )}`"
      ></span>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import "@/assets/styles/utils";

.carousel {
  position: relative;
  max-width: 1440px;
  margin: auto;
  overflow: hidden;

  &__scroller {
    margin: 0 20px;
    // max-width: 1440px;
    display: flex;
    gap: 24px;
    overflow-x: scroll;
    scroll-snap-type: x mandatory;
    scroll-snap-stop: always;
    -webkit-overflow-scrolling: touch;
    -ms-overflow-style: none; /* Internet Explorer 10+ */
    scrollbar-width: none; /* Firefox */

    &::-webkit-scrollbar {
      display: none; /* Safari and Chrome */
    }

    @include start-from(phone) {
      gap: 0;
      margin: auto;
    }
  }

  &__navigation-container {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    // max-width: 100%;
    height: calc(100% - 20px);
    pointer-events: none;

    button {
      position: relative;
      border-radius: 100%;
      width: 40px;
      height: 40px;
      border: 0;
      cursor: pointer;
      pointer-events: all;

      @include start-from(phone) {
        display: none;
      }

      &.inactive {
        opacity: 0;
        pointer-events: none;
      }

      i {
        color: rgb(var(--secondary));
        font-size: 1.2rem;
      }
    }
  }

  &__pagination-container {
    position: absolute;
    display: flex;
    gap: 10px;
    justify-content: center;
    align-items: center;
    bottom: 0;
    height: 40px;
    width: 100%;

    @include start-from(generic-desktop) {
      display: none;
    }

    .photo {
      background-color: rgba(var(--neutral));
    }

    span {
      display: block;
      height: 10px;
      width: 10px;
      background-color: rgba(0, 0, 0, 1);
      opacity: 0.2;
      border-radius: 100%;
      transition: all 0.15s;

      &.active {
        opacity: 0.7;
        transform: scale(1.2);
      }
    }
  }
}
</style>

<style lang="scss">
@import "@/assets/styles/utils";

.card {
  scroll-snap-align: start;
}
</style>
