<template>
  <div>
    <h5>Hello</h5>
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
    </div>


    <p class="text-lg text-orange-600">
      ... Ending ...
    </p>

    <button @click="awesome = !awesome" class="btn">Toggle</button>
    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Oh no 😢</h1>


  <button @click="counts++" class="bg-violet-500 hover:bg-violet-600 focus:outline-none focus:ring focus:ring-violet-300 active:bg-violet-700 px-5 py-2 text-sm leading-5 rounded-full font-semibold text-white">Add 1</button>
  <p>Count is: {{ counts }}</p>

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { SpreadsheetHelper } from "sbp-google-sheet-helper";

const products = ref([]);
const awesome = ref();
const counts = ref(0);

// lifecycle hooks
onMounted(async () => {

  const helper = new SpreadsheetHelper({
    fileId: "1vxWFUYZwMuDtmvoxaVvkK7KYe4muoEJ0_cqA6wmaaS4",
    deployKey: "AKfycbxg5YvKVt_soNbwetrXWTFEqkwI4LVgpbLUrEGiBkCxR1gwuhsMY24Rc_Td2ydZPKNAIA",
  });

  const response = await helper.sheet("table1").getData("delmode", 0);
  console.log(response);

  products.value = response.data


})
</script>