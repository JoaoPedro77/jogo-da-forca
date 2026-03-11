<template>
  <div>Redirecionando...</div>
</template>

<script setup>
import { navigateTo } from 'nuxt/app';

navigateTo('/?aba=party', { replace: true });
</script>
