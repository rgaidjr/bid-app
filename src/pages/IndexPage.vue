<template>
  <q-page class="flex flex-center">
    <div class="responsive-logo-container">
      <img
        alt="BID logo"
        src="~assets/bid-logo.jpg"
        class="responsive-logo"
      >
    </div>
  </q-page>
</template>

<script setup>
//
</script>

<style scoped>
.responsive-logo-container {
  width: 100%;
  max-width: 600px;
  padding: 20px;
  display: flex;
  justify-content: center;
}

.responsive-logo {
  width: 100%;
  height: auto;
  max-width: 100%;
  object-fit: contain;
  transition: transform 0.3s ease;
}

/* Add a subtle zoom effect on hover */
.responsive-logo:hover {
  transform: scale(1.02);
}

/* Media queries for different screen sizes */
@media (max-width: 768px) {
  .responsive-logo-container {
    max-width: 400px;
  }
}

@media (max-width: 480px) {
  .responsive-logo-container {
    max-width: 280px;
    padding: 15px;
  }
}
</style>
