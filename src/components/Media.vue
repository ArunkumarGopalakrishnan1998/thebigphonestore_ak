<script setup >

import { defineProps } from 'vue';
import {ref,  onMounted, onBeforeUnmount } from 'vue'
import Viewer from './Viewer.vue'
import Video from './Video.vue'

const props = defineProps(['productImages']);
let media_files = ref([]),
     active_media = ref({}),
     active_index = ref(0),
     zoom_result = '',
     zoom_lens = '',
     active_img = '',
     cx = '',
     cy = '',
     isMobile = ref(window.innerWidth <= 768),
     viewer_images = ref(props.productImages.filter((item) => !item.video));


function handleResize() {
  isMobile.value = window.innerWidth <= 768;

  if (isMobile.value) {
    media_files.value = props.productImages.filter((item) => !item.video);
    active_media.value = media_files.value[0];
    active_index.value = media_files.value.findIndex(item => item === active_media.value);
  } else {
     media_files.value = props.productImages;
     active_media.value = media_files.value[0];
     active_index.value = media_files.value.findIndex(item => item === active_media.value)
  }
};

onMounted(() => {
  window.addEventListener('resize', handleResize);
  handleResize();
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', handleResize);
});

function setZoomEvents() {
    
        zoom_result = document.getElementById('zoom-result'),
        zoom_lens = document.getElementById('zoom-lens'),
        active_img = document.getElementById('active-img');

        zoom_lens.addEventListener("mousemove", moveLens);
        active_img.addEventListener("mousemove", moveLens); 

        active_img.addEventListener('mouseenter', () => {
            if (!isMobile.value) {
            active_img.parentElement.insertBefore(zoom_lens, active_img);
            zoom_result.style.visibility='visible';
            zoom_lens.style.visibility='visible';
            zoom_result.style.width = zoom_lens.offsetWidth * 4 + 'px';
            zoom_result.style.height = zoom_lens.offsetHeight * 4 + 'px';

            cx = zoom_result.offsetWidth / zoom_lens.offsetWidth;
            cy = zoom_result.offsetHeight / zoom_lens.offsetHeight;

            let large_img = document.createElement("img");
            large_img.setAttribute('src', active_media.value.large);

            zoom_result.style.backgroundImage = "url('" + large_img.src  + "')";
            zoom_result.style.backgroundRepeat = 'no-repeat',
            zoom_result.style.backgroundSize = (large_img.width * cx)/(large_img.width/active_img.width) + "px " + (large_img.height * cy)/(large_img.height/active_img.height) + "px";
            }
        });

        zoom_lens.addEventListener('mouseleave', () => {
            zoom_lens.style.visibility='hidden';
            zoom_result.style.visibility='hidden';
        });
}
function moveLens(e) {
    let cursor_pos = '',
        cursor_x = '',
        cursor_y = '';
    e.preventDefault();
    
    cursor_pos = getCursorPos(e);
    cursor_x = cursor_pos.x - (zoom_lens.offsetWidth / 2);
    cursor_y = cursor_pos.y - (zoom_lens.offsetHeight / 2);
    if (cursor_x > active_img.width - zoom_lens.offsetWidth) {
        cursor_x = active_img.width - zoom_lens.offsetWidth;
    }
    if (cursor_x < 0) {
        cursor_x = 0;
    }
    if (cursor_y > active_img.height - zoom_lens.offsetHeight) {
        cursor_y = active_img.height - zoom_lens.offsetHeight;
    }
    if (cursor_y < 0) {
        cursor_y = 0;
    }
    
    zoom_lens.style.left = cursor_x + active_img.getBoundingClientRect().left + "px";
    zoom_lens.style.top = cursor_y + active_img.getBoundingClientRect().top +  "px"; 
    zoom_result.style.backgroundPosition = "-" + (cursor_x * cx) + "px -" + (cursor_y * cy) + "px";
}

function getCursorPos(e) {
    var a, x = 0, y = 0;
    e = e || window.event;
    a = active_img.getBoundingClientRect();
    x = e.pageX - a.left;
    y = e.pageY - a.top;
    x = x - window.pageXOffset;
    y = y - window.pageYOffset;
    return {x : x, y : y};
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

let show_viewer = ref(false),
    toggleViewer = () => {
        show_viewer.value = !show_viewer.value;
        if(show_viewer.value) {
            document.getElementsByTagName('body')[0].style.overflow = 'hidden';
        } else {
            document.getElementsByTagName('body')[0].style.overflow = 'visible';
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

</script>

<template>

    <div class="flex-col md:w-1/2 lg:w-2/5">
        <div class="flex flex-col md:justify-end items-center bg-white shadow">
            <div class="flex justify-between items-center w-full md:pt-12 md:pb-8">
                <div class="p-4 cursor-pointer" @click="previous">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5 8.25 12l7.5-7.5" />
                    </svg>
                </div>
 
                <div class="py-4 md:py-0">
                    <div @touchstart="handleStart" @touchmove="handleMove" @touchend="handleEnd">
                        <img v-if="!active_media.video" id="active-img" class="h-[480px] md:h-96 object-contain" v-bind:src="active_media.original" @click="toggleViewer()" alt="product_image" v-on:load="setZoomEvents">
                        <Video v-else v-bind:video="active_media" class="h-80 md:h-96 object-contain" v-bind:callback="() => {toggleViewer();}"/>
                    </div>
                </div>
                
                <div id="zoom-lens" class="invisible absolute border bg-cyan-500 opacity-25 cursor-pointer w-[10%] h-[20%]" @click="toggleViewer()"></div>
                <div id="zoom-result" class="invisible top-20 left-1/2 absolute invisible border border-black shadow-2xl"></div>
                
                <div class="p-4 cursor-pointer" @click="next">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                        <path stroke-linecap="round" stroke-linejoin="round" d="m8.25 4.5 7.5 7.5-7.5 7.5" />
                    </svg>  
                </div>
            </div>
            <div class="hidden md:visible md:flex md:justify-center md:w-full md:pb-4 ">
                <div v-for="(item, index) in media_files" class="mt-8 mx-4 cursor-pointer">
                    <img v-if="!item.video" v-bind:class="['md:object-contain p-2', index === active_index ? 'border-2 border-teal-500' : '']" v-bind:src="item.thumbnail" alt="product_image" @click="switchMedia(item, index)">
                    <Video v-else v-bind:video="item" v-bind:class="['md:object-contain p-2 w-24', index === active_index ? 'border-2 border-teal-500' : '']" v-bind:callback="() => {switchMedia(item, index);}"/>
                </div>
            </div>

        </div>
        <div class="flex items-center justify-center py-4">
            <div v-for="(item, index) in media_files" class="cursor-pointer" @click="switchMedia(item, index)">
                <div v-bind:class="['w-2 h-2 mx-1 rounded-full border', index === active_index ? 'bg-orange-500' : 'bg-black']"></div>
            </div>
        </div>
    </div>

    <div v-if="show_viewer" id="viewer" class="absolute bg-black/[0.8] h-full w-full flex items-center justify-center">
        
        <Viewer v-bind:mediaFiles="media_files" v-bind:toggleViewer="toggleViewer" v-bind:activeMedia="active_media" v-bind:isMobile="isMobile"/>

    </div>

</template>