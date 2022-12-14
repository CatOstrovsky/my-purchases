<template>
  <ul class="purchase-list">
    <order v-for="(order, index) in orders"
           :key="index"
           :order="order"
           @update="event => UpdateOrder(index, event)"
           @onDelete="event => DeleteOrder(index, event)"
    />
  </ul>
  <div style="text-align: center">
    <button class="button"  @click.prevent="AddOrder">Добавить заказ</button>
  </div>
</template>

<script setup>
import order from "@/components/order.vue"
import { reactive, onMounted } from "vue"

let orders = reactive([]);

function Save() {
  window.localStorage.setItem("orders", JSON.stringify(orders))
}

function Load() {
  let items = window.localStorage.getItem("orders");
  if(!!items) {
    orders = Object.assign(orders, JSON.parse(items));
  }
}

onMounted(() => Load());

function UpdateOrder(index, order) {
  orders[index] = order;
  Save();
}

function DeleteOrder(index, $event) {
  orders.splice(index, 1);
  Save();
}

function AddOrder($event) {
  orders.push({
    title : "Заказ от "+new Date().toLocaleString(),
    products: [{
      name: "",
      price: 0,
      count: 1,
      user: "Я"
    }],
    helperPercent: 10,
    packaging: 200,
    shipping: 800
  });
  Save();
}
</script>

<style lang="scss">
.button {
  background: #fff;
  color: #000;
  border-radius: 20px;
  padding: 10px 15px;
  cursor: pointer;
  border: 0px;

  &:hover {
   opacity: .7;
  }

  &-danger {
    background: lightcoral;
    color: #fff;
  }
}
</style>