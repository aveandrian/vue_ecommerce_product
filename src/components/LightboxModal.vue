<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faXmark } from '@fortawesome/free-solid-svg-icons'

const props = defineProps(['productData', 'toggleLightboxModalOpen', 'selectedIndex', 'prev', 'next'])
</script>

<template>
    <div  class='modal-wrapper'>
        <div class='modal-content'>
        <FontAwesomeIcon  alt="Close modal" size='2xl' :icon="faXmark" @click="toggleLightboxModalOpen" class='close-modal'/>
        <div class='main-img-wrapper'>
            <img class='main-img' :src="productData.images[selectedIndex].main" />
            <div v-if="selectedIndex != 0" @click="prev" class='prev-img-container'><img src='/images/icon-previous.svg' class='prev-img'/></div>
            <div v-if="selectedIndex < productData.images.length - 1" @click="next" class='next-img-container'><img src='/images/icon-next.svg' class='next-img'/></div>
        </div>
        <div class='preview-imgs'>
            <div v-for="(image, i) in productData.images" 
                :key="i"
                @click="selectedIndex=i" 
                :class="`thumbnail-container ${selectedIndex == i ? 'selected' : ''}`"
            >
                <img :src="image.thumbnail" />
            </div>
        </div>
        </div>
    </div>
</template>

