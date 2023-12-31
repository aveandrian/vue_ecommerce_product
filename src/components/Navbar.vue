<script setup>
import { ref, computed } from 'vue';
import { faCartShopping, faTrashCan } from '@fortawesome/free-solid-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'


const props = defineProps(['cartItems', 'removeItemFromCart'])

const isOpened = ref(false)
const cartIsOpened = ref(false)

const itemsInCart = computed(()=>{
  if(props.cartItems.length > 0){
      let sum = 0;
      props.cartItems.forEach(item => sum += item.amount)
      return sum
  }
  return 0
})

function toggleIsOpened(){
  cartIsOpened.value = false
  isOpened.value = !isOpened.value
}
function toggleCartIsOpened(){
  cartIsOpened.value = !cartIsOpened.value
}
</script>

<template>
 <nav>
    <img 
        :src="`/images/icon-${isOpened ? 'close' : 'menu'}.svg`"
        :class="`menu-btn ${isOpened ? 'opened' : ''}`"
        alt='Menu button' 
        @click="toggleIsOpened"
    />
    <img class='logo'  alt='Logo' src='/images/logo.svg' />
    <div :class="`nav-items-back ${isOpened ? 'opened' : ''}`" @click="toggleIsOpened"></div>
    <div :class="`nav-items ${isOpened ? 'opened' : ''}`">
        <a>Collections</a>
        <a>Men</a>
        <a>Women</a>
        <a>About</a>
        <a>Contact</a>
    </div>
    <div class='cart-wrapper' @click="toggleCartIsOpened">
        <FontAwesomeIcon :icon="faCartShopping" class='cart-icon'  alt='Cart button' />
        <div v-if="cartItems.length > 0" class='cart-items-amount'>{{itemsInCart}}</div>
       
            <div v-if="cartIsOpened" class='cart-container'>
                <p class='cart-container-title'>Cart</p>
                <div class='cart-container-divider' ></div>
                <div class='cart-container-items'>
                  <div v-for="cartItem in cartItems" class='cart-item-container' :key="cartItem.id">
                    <img class='cart-item-img' :src="cartItem.image" />
                    <div class='cart-item-description'>
                      <p class='cart-item-title'>{{cartItem.name}}</p>
                      <p class='cart-item-price'>${{cartItem.price}} x {{cartItem.amount}} <span class='total-price'>${{parseFloat(cartItem.price * cartItem.amount).toFixed(2)}}</span></p>
                  </div>
                  <FontAwesomeIcon :icon="faTrashCan" class='delete-item-icon' @click="() => removeItemFromCart(cartItem.id)"/>
                </div>
                <button v-if="cartItems.length > 0" class='checkout-btn'>Checkout</button>
                <p v-if="cartItems.length == 0" class='cart-empty-text'>Your cart is empty</p>
                </div>
            </div>

    </div>
    <img class='avatar' alt='User avatar' src='/images/image-avatar.png' />
  </nav>
</template>

<style>
nav {
    display: flex;
    align-items: center;
    height: 12vh;
    border-bottom: 0.063rem solid hsl(220, 14%, 75%);
}

.logo {
    margin-right: 5vw;
}

.menu-btn {
    display: none;
    position: absolute;
    margin-right: 2vw;
}


.nav-items {
    display: flex;
    align-items: center;
    gap: 2vw;
    height: 100%;
}

.nav-items a {
    display: flex;
    align-items: center;
    height: 100%;
    position: relative;
}

.nav-items a:hover {
    cursor: pointer;
}

.nav-items a:hover::after {
    content: "";
    position: absolute;
    bottom: 0;
    left: 0;
    height: 0.313rem;
    width: 100%;
    background-color: hsl(26, 100%, 55%);
} 

.nav-items-back {
    display: none;
    position: fixed;
    top:0;
    left:0;
    width: 100vw;
    height: 100vh;
    background-color: rgba(0, 0, 0, 0.5);
    opacity: 0;
}

.cart-icon {
    color: hsl(220, 14%, 75%);
}

.cart-icon:hover {
    cursor: pointer;
    color: hsl(220, 13%, 13%);
}

.cart-wrapper {
    margin-left: auto;
    margin-right: 2vw;
    position: relative;
}

.cart-items-amount {
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    height: 0.75rem;
    width: 0.938rem;
    top: -20%;
    right: -40%;
    font-size: 0.5rem;
    font-weight: 700;
    border-radius: 40%;
    background-color: hsl(26, 100%, 55%);
    color: white;
}

.cart-container {
    z-index: 1;
    position: absolute;
    top: 200%;
    left: -10vw;
    width: 25vw;
    min-height: 25vh;
    border-radius: 0.625rem;
    background-color: white;
    box-shadow: 0 0.625rem 0.938rem 0 rgba(0, 0, 0, 0.2);
    display: flex;
    flex-direction: column;
}

.cart-container-title {
    margin: 5%;
    font-weight: 700;
}

.cart-container-divider {
    height: 0.063rem;
    width: 100%;
    background-color:  hsl(220, 14%, 75%);
}

.cart-container-items {
    padding: 5%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: inherit;
    height: inherit;
    gap: 1.25rem;
}

.cart-empty-text {
    align-self: center;
    color: hsl(219, 9%, 45%);
    font-weight: 700;
}

.cart-item-container {
    display: flex;
    gap: 0.625rem;
    align-items: center;
    color: hsl(219, 9%, 45%)
}

.cart-item-img {
    width: 20%;
    border-radius: 0.625rem;
}

.cart-item-description {
    height: 100%;
    display: flex;
    flex-direction: column;
    gap: 0.313rem;
}

.delete-item-icon {
    margin-left: auto;
    color: hsl(220, 14%, 75%);
}

.delete-item-icon:hover {
    cursor: pointer;
    color: hsl(220, 13%, 13%)
}

.total-price {
    color: hsl(220, 13%, 13%);
    font-weight: 700;
}

.checkout-btn {
    width: 100%;
    height: 3.125rem;
    border-radius: 0.625rem;
    border: none;
    color: white;
    font-weight: 700;
    background-color: hsl(26, 100%, 55%);
}

.checkout-btn:hover {
    cursor: pointer;
    background-color: hsla(26, 100%, 55%, 0.6);
}

.avatar {
    width: 3.125rem;
}

.avatar:hover {
    cursor: pointer;
    border: 0.125rem solid hsl(26, 100%, 55%);
    border-radius: 3.125rem;
}

@media screen and (max-width: 60rem) {
    nav {
        padding: 3%;
        padding-inline: 5%;
        border-bottom: none;
    }
    .menu-btn {
        display: block;
        z-index: 2;
    }

    .menu-btn.opened {
        position: fixed;
    }

    .logo {
        margin-left: 7vw;
    }

    .nav-items {
        z-index: 1;
        position: fixed;
        top: 0;
        left: 0;
        height: 100vh;
        width: 60vw;
        background-color: white;
        padding: 12vh 5vw;
        gap: 3vh;
        align-items: start;
        transition: 0.3s ease-in-out;
        transform: translateX(-100vw);
        flex-direction: column;
    }

    .nav-items-back {
        display: block;
        z-index: 0;
        transform: translateX(-100vw);
    }

    .nav-items-back.opened {
        z-index: 1;
        transition: 0.5s ease-in-out;
        opacity: 1;
        transform: none
    }

    .nav-items a {
        font-weight: 700;
        height: auto;
    }

    .nav-items a:hover::after  {
        display: none;
    }

    .nav-items.opened {
        transform: none
    } 
    
    .cart-wrapper {
        margin-right: 7vw;
    }
    
    .cart-container {
        width: 90vw;
        left: -70vw
    }

    .avatar {
        width: 1.875rem;
    }
}
</style>
