<template>
  <li class="purchase-list__item" :class="{'purchase-list__item-open' : isOpen}">
    <p class="purchase-list__item__title" @click.prevent="isOpen = !isOpen">
      <span>{{ props.order.title }}</span>
      <img src="/public/plus-svgrepo-com.svg" alt="" class="icon">
    </p>

    <div class="purchase-list__item__content">
      <table class="products-table">
        <thead>
        <tr>
          <th>Имя товара</th>
          <th>Цена</th>
          <th>Количество</th>
          <th>Заказчик</th>
          <th>Сумма</th>
          <th></th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(product, productIndex) in props.order.products" :key="productIndex">
          <td>
            <input type="text" v-model="product.name" @input="UpdateOrder">
          </td>
          <td>
            <input type="number" v-model="product.price" @input="UpdateOrder" style="max-width: 100px">
          </td>
          <td>
            <input type="number" v-model="product.count" @input="UpdateOrder" style="max-width: 100px">
          </td>
          <td>
            <select v-model="product.user" @change="updateOrder">
              <option v-for="(name, index) in users"
                      :key="index"
                      :value="name">
                {{ name }}
              </option>
            </select>
          </td>
          <td style="font-weight: bold">
            {{ CurrencyFormat(product.price * product.count) }}&#8381;
            <sup v-if="!!props.order.helperPercent">
              (+{{ parseInt((product.price * product.count) * (props.order.helperPercent / 100)) }}&#8381;)
            </sup>
          </td>
          <td>
            <a href="#" class="product-action" v-if="props.order.products.length !== 1" @click.prevent="$event => OnClickRemove(productIndex)">-</a>
            <a href="#"  class="product-action product-action-plus" @click.prevent="OnClickAdd"
               v-if="productIndex === props.order.products.length - 1">+</a>
          </td>
        </tr>
        </tbody>
      </table>

      <div class="order-option">
        <span>Процент посредника: </span>
        <input type="number" v-model="props.order.helperPercent" @input="UpdateOrder">%
        <sup>{{ CurrencyFormat(totalPrice * (props.order.helperPercent / 100)) }}&#8381;</sup>
      </div>
      <div class="order-option">
        <span>Упаковка: </span>
        <input type="number" v-model="props.order.packaging" @input="UpdateOrder">&#8381;
      </div>
      <div class="order-option">
        <span>Доставка: </span>
        <input type="number" v-model="props.order.shipping" @input="UpdateOrder">&#8381;
      </div>

      <div style="margin-bottom: 15px">
        <p class="total"><b>ИТОГО</b>: <span style="background: #2f9914; color: #fff; padding: 2px 7px;">{{ CurrencyFormat(totalPrice
          + (totalPrice * (props.order.helperPercent / 100))
          + props.order.packaging
          + props.order.shipping) }}&#8381;</span></p>
        <ul>
          <li>Товары: {{CurrencyFormat(totalPrice)}}&#8381</li>
          <li>Посредник: {{CurrencyFormat(totalPrice * (props.order.helperPercent / 100))}}&#8381</li>
          <li>Упаковка: {{props.order.packaging}}&#8381</li>
          <li>Доставка: {{CurrencyFormat(props.order.shipping) }}&#8381</li>
        </ul>
      </div>

      <table style="width: 100%;margin-bottom: 20px;" class="table-by-users">
        <thead>
          <tr>
            <th>Покупатель</th>
            <th>Выкуп</th>
            <th>Товары</th>
            <th>Посредник</th>
            <th>Упаковка</th>
            <th>Доставка</th>
            <th>Итого</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(user, name) in ordersByUsers">
            <td>{{name}}</td>
            <td>{{user.percent.toFixed(2)}}%</td>
            <td>{{user.totalText}} = <b>{{CurrencyFormat(user.total)}}&#8381</b></td>
            <td>{{ CurrencyFormat( user.helperPercent ) }}&#8381</td>
            <td>{{ CurrencyFormat( user.packaging ) }}&#8381</td>
            <td>{{ CurrencyFormat( user.shipping ) }}&#8381</td>
            <td><span style="background: #3c73ae; color: #fff; padding: 2px 7px;">{{ CurrencyFormat( user.total +  user.helperPercent + user.packaging + user.shipping ) }}&#8381</span></td>
          </tr>
        </tbody>
      </table>
      <button style="margin-bottom: 15px;float: right" class="button button-danger" @click.prevent="Delete">Удалить заказ</button>
    </div>
  </li>
</template>

<script setup>
import { ref, computed, onMounted  } from "vue"
import users from "@/users.json"

let props = defineProps(["order"]);
let emit = defineEmits(["update", "onDelete"]);
let isOpen = ref(false);
let totalPrice = ref(0);

let ordersByUsers = computed(() => {
  let output = { }
  for(let product of props.order.products) {
    if(!output[product.user])
      output[product.user] = { products: [], total: 0, percent: 0 };

    output[product.user].products.push(product);
    output[product.user].total = output[product.user].products
      .map(item => item.price * item.count)
      .reduce((partialSum, a) => partialSum + a, 0);

    output[product.user].totalText = output[product.user].products
      .map(item => item.price * item.count).join(" + ")

    output[product.user].percent = output[product.user].total / totalPrice.value;
    output[product.user].helperPercent = (totalPrice.value * (props.order.helperPercent / 100)) * output[product.user].percent;
    output[product.user].packaging = props.order.packaging * output[product.user].percent;
    output[product.user].shipping = props.order.shipping * output[product.user].percent;
  }

  return output;
})

function CalcTotalPrice() {
  totalPrice.value = props.order.products
    .map(item => item.price * item.count)
    .reduce((partialSum, a) => partialSum + a, 0);
}

onMounted(() => {
  CalcTotalPrice();
})

function CurrencyFormat(num) {
  return new Intl.NumberFormat("ru", {style: "decimal"}).format(num.toFixed(2));
}

function UpdateOrder($event) {
  emit("update", props.order);
  CalcTotalPrice();
}

function OnClickRemove(index, $event) {
  props.order.products.splice(index, 1);
  UpdateOrder($event);
}

function OnClickAdd($event) {
  props.order.products.push({
    name: "",
    price: 0,
    count: 1,
    user: "Я"
  })
  UpdateOrder($event);
}

function Delete() {
  if(confirm("Точно?"))
    emit("onDelete")
}
</script>

<style scoped lang="scss">
.table-by-users, .table-by-users th, .table-by-users td {
  border: 1px solid black;
  border-collapse: collapse;
  padding: 5px;
}

.total {
  font-size: 17px;
  margin: 10px 0px;
  padding-top: 10px;
  border-top: 1px #e7e7e7 solid;
}

.order-option {
  margin-bottom: 5px;
  font-size: 13px;

  input {
    border: 0px;
    font-weight: bold;
    max-width: 50px;
    outline: none!important;
    text-align: right;
  }
}

.product-action {
  text-decoration: none;
  font-size: 15px;
  background: #ff4747;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  line-height: 0px;
  color: #fff;
  margin-right: 10px;

  &:last-child {
    margin: 0px;
  }

  &-plus {
    background: #2f9914;
  }
}
</style>