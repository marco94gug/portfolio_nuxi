<template>
  <atomic-button
    class="arrow-up"
    name="Arrow Up"
    @click="() => handleArrowUpClick()"
    ><i class="pi pi-angle-up" id="arrow-icon"
  /></atomic-button>
</template>

<script setup lang="ts">
const handleArrowUpClick = () => {
  window.scrollTo({
    top: 0,
    behavior: "smooth",
  });
};
</script>

<style lang="scss" scoped>
.arrow-up {
  opacity: 0;
  pointer-events: none;
  position: fixed;
  z-index: 5;
  bottom: 5%;
  right: 5%;
  width: 60px;
  height: 60px;
  border-radius: 100%;
  font-size: 1.4rem;
  transition: all 0.25s;
  box-shadow: 0 0 12px rgba(var(--neutral), 0.2);

  #arrow-icon {
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>
