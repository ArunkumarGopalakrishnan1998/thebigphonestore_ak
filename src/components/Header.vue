<script setup lang="ts">

import logo from '../assets/tbps-logo.webp';
</script>

<template>
    <header class="bg-white">
        <nav class="p-5 w-full md:py-8 md:px-16 lg:px-32" aria-label="Global">
          <div class="flex lg:flex-1">
            <a href="#" class="-m-1.5 p-1.5">
              <img class="h-8 w-auto" v-bind:src="logo" alt="">
            </a>
          </div>
        </nav>
    </header>
</template>