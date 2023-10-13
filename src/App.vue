<script setup>
import Navbar from './components/Navbar.vue'
import LightboxModal from './components/LightboxModal.vue'
import productData from './productData.json'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faXmark, faPlus, faMinus, faCartShopping } from '@fortawesome/free-solid-svg-icons'
import { useMediaQuery } from '@vueuse/core'
import { ref } from 'vue';

const selectedIndex = ref(0)
const lightboxModalOpen = ref(false)
const countItem = ref(0)
const cartItems = ref([])

const isMobile = useMediaQuery('(max-width: 950px)')

function toggleLightboxModalOpen(){
  lightboxModalOpen.value = !lightboxModalOpen.value
}
function handleRemoveItem(){
  if(countItem.value == 0)
    return
  countItem.value--
}
function handleAddItem(){
  countItem.value++
}
function prev(){
  selectedIndex.value--
}
function next(){
  selectedIndex.value++
}
function addItemToCart(){
  if(countItem.value < 1)
    return;
  let productFound = false
  let tempArr = cartItems.value.map(item => {
    if(item.id == productData.id){
      productFound=true; 
      return {...item, amount: item.amount + countItem.value } 
    }
    else return item
  })
  cartItems.value = productFound ? tempArr : [...cartItems.value, {
    id: productData.id,
    image: productData.images[0].thumbnail,
    name: productData.name,
    price: productData.discount ? parseFloat(productData.price * productData.discount).toFixed(2) : productData.price.toFixed(2),
    amount: countItem.value
  }]
}

function removeItemFromCart(id){
  cartItems.value = cartItems.value.filter(item => item.id != id)
}
</script>

<template>
  <Navbar :cartItems="cartItems" :removeItemFromCart="removeItemFromCart"/>
  <div class='main-divider'></div>
  <main>
    <div v-if="isMobile" class='image-carousel'>
      <div class='images-container' :style="{transform: `translateX(-${selectedIndex * 100}%)`}">
        <div v-for="(image, i) in productData.images" :key="i" class='carousel-item'>
          <img class='carousel-item-img' :src="image.main"/>
        </div>
      </div>
      <div v-if="selectedIndex != 0" @click="prev" class='prev-img-container'><img src='/images/icon-previous.svg' class='prev-img'/></div>
      <div v-if="selectedIndex < productData.images.length - 1" @click="next" class='next-img-container'><img src='/images/icon-next.svg' class='next-img'/></div>
    </div>

    <div v-else class='image-section'>
      <img class='main-img' :src="productData.images[selectedIndex].main" @click="toggleLightboxModalOpen" />
      <div class='preview-imgs'>
        <div v-for="(image, i) in productData.images" :key="i" @click="selectedIndex=i" :class="`thumbnail-container ${selectedIndex == i ? 'selected' : ''}`">
          <img :src="image.thumbnail" />
        </div>
      </div>
    </div>
    <div class='product-description'>
      <p class='brand'>Sneaker Company</p>
      <h1 class='title'>{{productData.name}}</h1>
      <p class='decription'>{{productData.description}}</p>
      <div class='price-container'>
        <div class='price-wrapper'>
          <p class='price'>${{productData.discount ? parseFloat(productData.price * productData.discount).toFixed(2) : productData.price}}</p>
          <span v-if="productData.discount" class='discount'>{{productData.discount*100}}%</span>
        </div>
        <p v-if="productData.discount" class='old-price'>${{parseFloat(productData.price).toFixed(2)}}</p>
      </div>
      <div class='add-to-cart'>
        <div class='amount-container'>
          <div class='icon-container' @click="handleRemoveItem">
            <FontAwesomeIcon :icon="faMinus"   class='remove-item' src='/images/icon-minus.svg'  />
          </div>
          <p class='count-item'>{{countItem}}</p>
          <div class='icon-container' @click="handleAddItem">
            <FontAwesomeIcon :icon="faPlus" class='add-item' src='/images/icon-plus.svg'  />
          </div>
        </div>
        <button class='add-to-cart-btn' @click="addItemToCart">
          <FontAwesomeIcon :icon="faCartShopping"/>Add to cart
        </button>
      </div>
    </div>
  </main>
  <LightboxModal 
    v-if="lightboxModalOpen"
    :productData="productData"
    :toggleLightboxModalOpen="toggleLightboxModalOpen"
    :selectedIndex="selectedIndex"
    :prev="prev"
    :next="next"
  />
  <div class="attribution">
    Challenge by <a href="https://www.frontendmentor.io?ref=challenge" target="_blank">Frontend Mentor</a>. 
    Coded by <a href="https://github.com/aveandrian">aveandrian</a>.
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Kumbh Sans', sans-serif;
}

body {
  min-height: 100vh;
}

#app {
  padding-inline: 15vw;
}

main {
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
  padding: 1.875rem;
  gap: 6.25rem;
}

.image-section {
  display: flex;
  flex-direction: column;
  gap: 1.875rem;
}

.main-img, .thumbnail-container {
  border-radius: 0.625rem;
}

.main-img {
  width: 100%;
}

.main-img:hover {
  cursor: pointer;
}

.preview-imgs {
  display: flex;
  justify-content: space-between;
  gap: 0.8rem;
}

.thumbnail-container {
  overflow: hidden;
  width: 6.25rem;
  height: 6.25rem;
}

.thumbnail-container:hover img {
  cursor: pointer;
  filter: opacity(0.5)
}

.thumbnail-container img {
  width: 100%;
}

.thumbnail-container.selected {
  border: 0.188rem solid hsl(26, 100%, 55%);
  background-color: white;
}

.thumbnail-container.selected img {
  filter: opacity(40%);
}

.close-modal {
  color: white;
}

.close-modal:hover {
  cursor: pointer;
}


.modal-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  min-height: 100vh;
  height: fit-content;
  background-color: rgba(0, 0, 0, 0.75);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.25rem;
  width: 31.25rem;
  /* height: 100; */
}

.close-modal {
  width: 5%;
  margin-left: auto;
}

.main-img-wrapper {
  width: 100%;
  position: relative;
}

.prev-img-container, .next-img-container {
  position: absolute;
  top: 50%;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 3.125rem;
  height: 3.125rem;
  border-radius: 50%;
}

.prev-img-container:hover, .next-img-container:hover {
  cursor: pointer;
}

.prev-img-container {
  transform: translateX(-50%);
}

.next-img-container {
  right: 0;
  transform: translateX(50%);
}


.image-carousel {
  overflow: hidden;
  position: relative;
  width: 100vw;
  height: 45vh;
}


.images-container {
  white-space: nowrap;
  transition: transform 0.3s;
  width: 100%;
  height: 100%;
}

.carousel-item {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}

.carousel-item-img {
  object-fit: scale-down;
  width: 100vw;
}


.product-description {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 1.875rem;
}

.brand {
  font-size: 0.8rem;
  font-weight: 700;
  color: hsl(26, 100%, 55%);
  text-transform: uppercase;
}

.title {
  font-size: 2.2rem;
}



.decription {
  color: hsl(219, 9%, 45%);
}


.price-wrapper {
  display: flex;
  align-items: center;
  gap: 1vw;
  margin-bottom: 1vh;
}

.price {
  font-size: 1.5rem;
  width: fit-content;
  font-weight: 700;
}

.discount {
  padding-inline: 0.313rem;
  border-radius: 0.313rem;
  font-size: 0.8rem;
  font-weight: 700;
  background-color: hsl(25, 100%, 94%);
  color: hsl(26, 100%, 55%);
}

.old-price {
  font-size: 0.8rem;
  font-weight: 700;
  text-decoration: line-through;
  color: hsl(220, 14%, 75%);
}


.add-to-cart {
  display: flex;
  align-items: center;
  gap: 1.5vw
}

.amount-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 30%;
  height: 3.125rem;
  border-radius: 0.625rem;
  gap: 1vw;
  background-color: hsl(223, 64%, 98%);
}

.icon-container {
  width: 30%;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.remove-item, .add-item {
  color: hsl(26, 100%, 55%);
}

.icon-container:hover {
  cursor: pointer;
}

.icon-container:hover .remove-item, .icon-container:hover .add-item  {
  color: hsla(26, 100%, 55%, 0.5);
}


.count-item {
  font-size: 0.8rem;
  font-weight: 700;
}

.add-to-cart-btn {
  height: 3.125rem;
  width: 65%;
  border-radius: 0.625rem;
  border: none;
  color: white;
  background-color: hsl(26, 100%, 55%);
  box-shadow: 0 1.25rem 1.25rem 0 hsla(26, 100%, 55%, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.625rem;
}

.add-to-cart-btn:hover {
  cursor: pointer;
  background-color: hsla(26, 100%, 55%, 0.6);
}

.attribution, .attribution a {
  text-align: center;
  color: hsl(220, 14%, 75%);
  font-size: 0.8rem;
}

@media screen and (max-width: 76.875rem) {
  .add-to-cart {
    flex-direction: column;
    gap: 0.625rem
  }
  .amount-container, .add-to-cart-btn {
    width: 100%;
  }
}

@media screen and (max-width: 60rem) {
  #app {
    padding: 0;
  }

  main {
    z-index: 0;;
    width: 100%;
    padding-top: 0;
    display: grid;
    grid-template-columns: 1fr;
    gap: 1.25rem;
    padding-inline: 0;
  }

  .icon-container {
    width: 20%;
  }

  .product-description {
    padding-inline: 1.875rem;
  }

  .prev-img-container, .next-img-container {
    transform: none;
    transform: translateY(-50%);
    width: 2.5rem;
    height: 2.5rem;
  }
  .prev-img-container {
    left: 4vw
  }


  .next-img-container {
    right: 4vw
  }

  .prev-img-container img, .next-img-container img {
    width: 20%;
  }
}
</style>
