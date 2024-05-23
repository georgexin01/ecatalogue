<template>
  <div>
    <!-- <h5>Hello</h5>
    <p class="text-lg text-orange-600">
      ... Starting ...
    </p>
    <pre>{{ products[0] }}</pre>

    <div v-for="item in products">
    <li v-if="item.name === 'Hello'">
      <span>{{ item.name }} - ( IF Only )</span>
    </li>
    </div>

    <div id="app" class="p-6">
      <div class="flex flex-wrap -mx-3">
        <div v-for="item in products" class="w-full sm:w-1/2 px-3 mb-6">
          <div class="p-6 rounded-lg shadow-lg">
            <span class="text-lg font-bold block">{{ item.name }}</span>
            <span class="block">{{ item.description }}</span>
            <span class="block">{{ item.date }}</span>
          </div>
        </div>
      </div>
    </div> -->

    <!-- <button @click="awesome = !awesome" class="btn">Toggle</button>
    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Oh no 😢</h1>

  <button @click="counts++" class="bg-violet-500 hover:bg-violet-600 focus:outline-none focus:ring focus:ring-violet-300 active:bg-violet-700 px-5 py-2 text-sm leading-5 rounded-full font-semibold text-white">Add 1</button>
  <p>Count is: {{ counts }}</p> -->

    <div class="p-5 max-w-screen-md mx-auto">
      <div class="w-full sm:max-w-48 max-w-36 mx-auto mb-4">
        <img src="/images/logo-coke.png" alt="CoCa Cola">
      </div>
      <div class="flex flex-wrap -mx-3">
        <!-- Product 1 -->
        <div v-for="(items, index) in products" class="w-1/2 px-2 mb-4">
          <div class="border rounded-lg p-3">
            <div class="w-full">
              <img src="/images/product1.jpg" class="w-full">
            </div>
            <div class="p-0">
              <h4 class="sm:text-2xl text-lg font-bold mb-1 tracking-tight">{{ items.name }}</h4>
              <div class="sm:text-base text-sm text-gray-700 mb-1">
                <p v-html="formatDescription(items.description)"></p>
              </div>
              <h4 class="text-2xl font-bold text-blue-500">RM {{ formatPrice(items.price) }}</h4>
            </div>
            <div class="mt-3">
              <!-- Buy Now -->
              <button v-if="items.quantity == 0" @click="addProduct(items.id, 'add')" type="button"
                class="w-full text-base bg-blue-500 hover:bg-blue-400 active:bg-blue-400 px-4 py-3 leading-5 rounded-lg text-white transition-all">Buy
                Now</button>

              <!-- ants -->
              <div v-else class="flex w-full px-2 py-1 border border-gray-700 rounded-lg">
                <a-button @click="addProduct(items.id, 'minus')" class="text-lg font-bold py-0" type="text">-</a-button>
                <input type="text" v-model="items.quantity" :key="index"
                  class="w-full text-center font-bold appearance-none focus:outline-none">
                <a-button @click="addProduct(items.id, 'add')" class="text-lg font-bold py-0" type="text">+</a-button>
              </div>
            </div>
          </div>
        </div>
        <!-- End -->
      </div>
    </div>

    <div class="fixed w-full bottom-0 bg-white shadow-md shadow-black z-10 p-3">
      <div class="w-full max-w-screen-md mx-auto">
        <button v-if="totalPrice <= 0" type="button"
          class="w-full text-base bg-blue-500 hover:bg-blue-400 active:bg-blue-400 px-4 py-3 leading-5 rounded-lg text-white transition-all">Contact
          Us Now</button>
        <!-- Result -->
        <button v-else @click="newPayment()" type="button"
          class="w-full text-base bg-blue-500 hover:bg-blue-400 active:bg-blue-400 px-4 py-3 leading-5 rounded-lg text-white transition-all">Place
          Order Now (RM<span>{{ totalPrice.toFixed(2) }}</span>)</button>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { SpreadsheetHelper } from "sbp-google-sheet-helper";

const route = useRoute();

const products = ref([]);
const addtoCart = ref([]);
const totalPrice = ref(0);

// init
onMounted(async () => {

  const helper = new SpreadsheetHelper({
    fileId: "1K7O_LVQ9LQbFdbzAMdOeHyTpel8POHB9nnV5ZuuZmQY",
    deployKey: "AKfycbwS-sriAJv8So2fpmoKI0Zi2OswDGGZyxerVikF-yrLkrKdTJjaUK-j5mo0Kks6HdQ61w",
  });

  const response = await helper.sheet("productList").getData("name");
  console.log(response);

  products.value = response.data
  products.value.forEach(element => {
    element['quantity'] = 0;
  });

})

// Function to increment the quantity of a product
const addProduct = (id, type) => {
  const existingItem = products.value.find(item => item.id === id);
  if (existingItem) {
    if (type === 'add') {
      existingItem.quantity = parseFloat(existingItem.quantity) + 1;
      totalPrice.value += parseFloat(existingItem.price);
    } else if (existingItem.quantity > 0) {
      existingItem.quantity = parseFloat(existingItem.quantity) - 1;
      totalPrice.value -= parseFloat(existingItem.price);
    }
    products.value.quantity = existingItem.quantity;
  }
};
const newPayment = () => {
  const newpayment = products.value.filter(item => item.quantity > 0);
  // payment
  let text = 'I would like to buy ';
  newpayment.forEach((element) => {
    let price = parseFloat(element.quantity * element.price).toFixed(2)
    let description = `${element.name} x ${element.quantity} = RM ${price}`;
    text += `\n${description}`;
  })
  text += "\n\nTotal Price is RM " + parseFloat(totalPrice.value).toFixed(2) + " "
  if (route.query.ref) {
    text += "\n\nYour referral contact number is " + route.query.ref
  }
  let url = "https://wa.me/60167177082?text=" + encodeURIComponent(text);
  window.open(url);
};

// filter
const nbr2l = (value) => {
  if (!value) return '';
  return value.replace(/\n/g, '<br>');
};
const formatDescription = (description) => {
  return nbr2l(description);
};
const formatPrice = (price) => {
  return parseFloat(price).toFixed(2);
};
</script>