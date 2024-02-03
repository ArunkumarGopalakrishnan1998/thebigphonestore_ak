<script setup>

import { defineProps } from 'vue';
import {ref,  onMounted } from 'vue'
import Video from './Video.vue'

let props = defineProps(['mediaFiles', 'toggleViewer', 'activeMedia', 'isMobile']);

let media_files = ref([]),
    active_media = ref(props.activeMedia),
    active_index = ref(0),
    selected_media_type = ref('');

    if (active_media.value.video) {
        selected_media_type.value = 'videos';
        media_files.value = props.mediaFiles.filter((item) => item.video);
        active_index.value = media_files.value.findIndex(item => item === active_media.value);
    } else {
        selected_media_type.value = 'photos';
        media_files.value = props.mediaFiles.filter((item) => !item.video);
        active_index.value = media_files.value.findIndex(item => item === active_media.value);
    }

function next() { 
    let new_active_index = media_files.value.findIndex(item => item === active_media.value);
    if (new_active_index < media_files.value.length - 1) {
        active_media.value = media_files.value[new_active_index + 1];
        active_index.value = new_active_index + 1;
    }
}

function previous() {
    let new_active_index = media_files.value.findIndex(item => item === active_media.value);
    if (new_active_index > 0) {
        active_media.value = media_files.value[new_active_index - 1];
        active_index.value = new_active_index - 1;
    }
}

function switchMedia(item, index) {
    
    active_media.value = item;
    active_index.value = index;
}
function switchMediaType(type) {
    if (type == 'videos') {
        selected_media_type.value = 'videos';
        media_files.value = props.mediaFiles.filter((item) => item.video);
        active_media.value = media_files.value[0];
        active_index.value = 0;
    } else {
        selected_media_type.value = 'photos';
        media_files.value = props.mediaFiles.filter((item) => !item.video);
        active_media.value = media_files.value[0];
        active_index.value = 0;
    }
}

let startX = ref(0),
      endX = ref(0),
      handleStart = (event) => {
            startX.value = event.clientX || event.touches[0].clientX;
      },
      handleMove = (event) => {
            endX.value = event.clientX || event.touches[0].clientX;
      },
      handleEnd = () => {
            const deltaX = endX.value - startX.value;

            if (deltaX > 0) {
                previous();
            } else if (deltaX < 0) {
                next();
            }
      };

onMounted(() => {
    let viewer_parent = document.getElementById('viewer');
    viewer_parent.style.top = window.scrollY + 'px';
    viewer_parent.style.left = 0;
    image_holder = document.getElementById('image_holder');
    image_container = document.getElementById('image_container');

})

let zoomLevel = ref(1);
let offsetX = ref(0);
let offsetY = ref(0);
let image_holder = document.getElementById('image_holder');
let image_container = document.getElementById('image_container');

let oldX = 0,
    oldY = 0;

const handleClick = (event) => {
    if (!props.isMobile) {
        if (zoomLevel.value === 1) {
            zoomLevel.value = 3;
            oldX = event.clientX;
            oldY = event.clientY;
            updateStyles();
        } else {
            zoomLevel.value = 1;
            offsetY.value =  0;
            offsetX.value =  0;
            updateStyles();
        }
    }
};

const handleMouseMove = (event) => {
    if (!props.isMobile) {
        if (zoomLevel.value === 3) {
            let xdiff = oldX - event.clientX ,
                ydiff = oldY - event.clientY ;

            if (document.getElementById('image_holder').getBoundingClientRect().left <= event.clientX  
                && document.getElementById('image_holder').getBoundingClientRect().right >= event.clientX) {
                offsetX.value =  xdiff * 3;
            }
            if (document.getElementById('image_holder').getBoundingClientRect().top <= event.clientY 
                && document.getElementById('image_holder').getBoundingClientRect().bottom >= event.clientY) {
                    offsetY.value =  ydiff * 3;
            }

            updateStyles();
        }
    }
};

const updateStyles = () => {
  const transformValue = `scale(${zoomLevel.value}) translate(${offsetX.value}px, ${offsetY.value}px)`;
  image_holder.style.transform = transformValue;
//   console.log()
};

</script>

<template>
    <div class="bg-white shadow w-full h-full md:h-[600px] md:m-8 rounded flex flex-col justify-center">
        <div class="absolute top-4 left-4 md:invisible">
            <button class="cursor-pointer py-1 m-4" @click="props.toggleViewer()">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12" />
                </svg>
            </button>
        </div>
        <div class="invisible hidden md:visible md:flex md:flex-col md:divide md:px-12">
            <div class="md:flex md:justify-between">
                <div class="mx-12 md:left">
                    <button class="border-b-4 px-4 py-1" v-bind:class="[selected_media_type === 'photos' ? 'border-teal-500' : 'hover:border-teal-100']" @click="switchMediaType('photos')">Photos</button>
                    <button class="border-b-4 px-4 py-1" v-bind:class="[selected_media_type === 'videos' ? 'border-teal-500' : 'hover:border-teal-100']" @click="switchMediaType('videos')">Videos</button>
                </div>
                <button class="px-4 py-1 pr-4 cursor-pointer" @click="props.toggleViewer()">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>
            <hr class="h-px bg-gray-200 border-0 dark:bg-gray-700">
        </div>
        <div class="md:flex md:justify-between md:p-12">
            <div class="flex flex-col items-center md:w-2/3">
                
                <div class="flex items-center w-full justify-between md:px-4">
                    <div class="cursor-pointer p-4 md:pr-4" @click="previous">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5 8.25 12l7.5-7.5" />
                        </svg>
                    </div>
                    <div id="image_container"  v-bind:class="[ !props.isMobile && !active_media.video ? (zoomLevel === 1 ? 'cursor-zoom-in' : 'cursor-zoom-out') : '']"  @click="handleClick" @mousemove="handleMouseMove" @touchstart="handleStart" @touchmove="handleMove" @touchend="handleEnd" style="overflow: hidden" class="w-full h-full flex items-center justify-center">
                        <img id="image_holder" v-if="!active_media.video" class="h-[400px] md:h-[400px] object-contain" v-bind:src="active_media.large"/>
                        <Video v-else v-bind:video="active_media" v-bind:playVideo="props.activeMedia.video ? true : false" class="h-80 md:h-[400px] object-contain"/>
                    </div>
                    <div class="cursor-pointer p-4 md:pl-4" @click="next">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                            <path stroke-linecap="round" stroke-linejoin="round" d="m8.25 4.5 7.5 7.5-7.5 7.5" />
                        </svg>  
                    </div>
                </div>
                
                <div class="flex mt-8 visible absolute bottom-4 md:invisible md:hidden">
                    <div v-for="(item, index) in media_files" class="">
                        <img v-if="!item.video" v-bind:src="item.thumbnail" v-bind:class="['md:object-contain p-2 mr-2', index === active_index ? 'border-2 border-teal-500' : 'border-2']" @click="switchMedia(item, index)"/>
                        <Video v-else v-bind:video="item" v-bind:class="['md:object-contain p-2 w-24 mr-2', index === active_index ? 'border-2 border-teal-500' : 'border-2']" v-bind:callback="() => {switchMedia(item, index);}"/>
                    </div>
                </div>
            </div>

            <div class="invisible hidden md:flex flex-col md:visible md:w-1/3 md:pr-8">
                <div class="font-medium text-xl">
                    <span>Samsung Galaxy S23 Plus 5G | Dual Sim | Like New | Lavender | 512 GB</span>
                </div>
                <div class="flex pt-8">
                    <div v-for="(item, index) in media_files" class="">
                        <img v-if="!item.video" v-bind:src="item.thumbnail" v-bind:class="['md:object-contain p-2', index === active_index ? 'border-2 border-teal-500' : '']" @click="switchMedia(item, index)"/>
                        <Video v-else v-bind:video="item" v-bind:class="['md:object-contain p-2 w-24', index === active_index ? 'border-2 border-teal-500' : '']" v-bind:callback="() => {switchMedia(item, index);}"/>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template> 