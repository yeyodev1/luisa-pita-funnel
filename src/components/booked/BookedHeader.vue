<script setup lang="ts">
</script>

<template>
  <header class="booked-header">
    <h2 class="booked-header__logo-text">LUISA PITA BEJARANO</h2>
  </header>
</template>

<style lang="scss" scoped>
@use '@/styles/colorVariables.module.scss' as colors;
@use '@/styles/fonts.modules.scss' as fonts;

.booked-header {
  width: 100%;
  padding: 0.9rem 1.5rem;
  display: flex;
  justify-content: center;
  background: #ffffff;
  border-bottom: 1px solid #D1FAE5;
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: 0 1px 8px rgba(22, 199, 132, 0.05);

  &__logo-text {
    font-family: fonts.$font-principal;
    font-weight: 800;
    font-size: 1.1rem;
    letter-spacing: 0.05em;
    color: colors.$LPB-DARK;
    margin: 0;
  }
}
</style>
