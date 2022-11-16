<script setup>
defineProps({
  nama: {
    type: String,
    default: "Dzun Nurroin",
    required: true,
  },
  role: String,
});
</script>

<template>
  <section class="hero">
    <img src="../assets/developer.png" alt="" height="250" />
    <div class="wrapper">
      <h1>{{ nama }}</h1>
      <p>{{ role }}</p>
      <div>
        <button>Download CV</button>
        <button>Hire Me</button>
      </div>
    </div>
  </section>
</template>
