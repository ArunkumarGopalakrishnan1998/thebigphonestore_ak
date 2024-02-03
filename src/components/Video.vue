<script setup>
import { defineProps, ref } from 'vue';

const props = defineProps(['video', 'callback', 'playVideo']);
const video_file = props.video,
      video = ref(video_file.video),
      thumbnail = ref(video_file.thumbnail),
      callback = ref(props.callback),
      show_video = ref(false);

      if (props.playVideo) {
        show_video.value = true;
      }
      function toggleVideo() {
        show_video.value = !show_video.value
      }
</script>

<template>
  <div class="flex items-center justify-center" @click="callback ? callback() : toggleVideo()">
    <img v-if="!show_video" id="thumbnail" alt="Video Thumbnail" class="opacity-45" :src="thumbnail"/>
    <div v-if="!show_video" class="absolute" id="play">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-12 h-12">
            <path stroke-linecap="round" stroke-linejoin="round" d="M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z" />
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.91 11.672a.375.375 0 0 1 0 .656l-5.603 3.113a.375.375 0 0 1-.557-.328V8.887c0-.286.307-.466.557-.327l5.603 3.112Z" />
        </svg>   
    </div>  
    <video v-if="show_video" v-bind:src="video" autoplay controls/> 
</div>
</template>
  