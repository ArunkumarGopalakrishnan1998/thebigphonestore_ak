<script setup>
import product_image from '../assets/product-image.jpeg';
import product_image_thumbnail from '../assets/product-image-thumbnail.jpeg';
import product_image_large from '../assets/product-image-large.jpeg';
import product_image_front from '../assets/product-image-front.jpeg';
import product_image_front_thumbnail from '../assets/product-image-front-thumbnail.jpeg';
import product_image_front_large from '../assets/product-image-front-large.jpeg';
import product_image_back from '../assets/product-image-back.jpeg';
import product_image_back_thumbnail from '../assets/product-image-back-thumbnail.jpeg';
import product_image_back_large from '../assets/product-image-back-large.jpeg';
import sample_video_thumbnail from '../assets/Sample-video-thumbnail.png';
import sample_video from '../assets/videos/Sample.mov';

import Media from './Media.vue';
let productImages = [
    {
        'original': product_image,
        'thumbnail': product_image_thumbnail,
        'large': product_image_large
    },
    {
        'original': product_image_front,
        'thumbnail': product_image_front_thumbnail,
        'large': product_image_front_large
    },
    {
        'original': product_image_back,
        'thumbnail': product_image_back_thumbnail,
        'large': product_image_back_large
    },
    {
        'video': sample_video,
        'thumbnail': sample_video_thumbnail
    }

];

</script>
    
<template>

    <div class="flex flex-col bg-zinc-100 p-4 md:justify-evenly md:items-center md:flex-row md:p-8">
        
        <Media v-bind:productImages="productImages" v-bind:toggleViewer="toggleViewer"/>

        <div class="md:w-2/5">
        <div class="divide-y-2 divide-zinc-200 md:divide-y-4">
            
            <div class="">
                <div class="flex justify-between text-xs md:text-sm">
                    <span class="text-zinc-400">SAMSUNG</span>
                    <span class="text-teal-600">Hurry Limited Stock</span>
                </div>
                <div class="flex flex-col">
                    <span class="text-sm my-2 font-medium md:text-2xl md:mb-5">Samsung Galaxy S23 Plus 5G</span>
                    <span class="text-lg mb-2 font-bold md:text-xl">£880.00</span>
                </div>

            </div>

            <div class="my-2">
                <div class="my-2 md:my-5">
                    <span class="text-sm font-medium md:text-sm">Sim:</span><span class="text-sm ml-1 font-thin md:text-sm">DUAL SIM</span>
                </div>
                <div class="my-2 md:my-5">
                    <span class="text-sm font-medium md:text-sm">Condition:</span><span class="text-sm ml-1 font-thin md:text-sm">LIKE NEW</span>
                </div>
                <div class="my-2 md:my-5">
                    <span class="text-sm font-medium md:text-sm">Color:</span><span class="text-sm ml-1 font-thin md:text-sm">LAVANDER</span>
                </div>
                <div class="my-2 md:my-5">
                    <span class="text-sm font-medium md:text-sm">Network:</span><span class="text-sm ml-1 font-thin md:text-sm">UNLOCKED</span>
                </div>
                <div class="my-2 md:my-5">
                    <span class="text-sm font-medium md:text-sm">Storage:</span><span class="text-sm ml-1 font-thin md:text-sm">512GB</span>
                </div>
            </div>
           
            <div class="flex items-center justify-center py-4 md:justify-start">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M5 12h14" />
                </svg>
                  
                <div class="px-3 py-1 mx-3 bg-zinc-500">
                    1
                </div>
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15" />
                </svg>
                  
            </div>
        </div>
        <div class="pb-3">
            <button class="bg-amber-300 px-1 py-2 w-full rounded-sm">Add to Basket</button>
        </div>
    </div>
    </div>      
</template>