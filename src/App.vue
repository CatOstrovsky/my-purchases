<template>
  <div class="container">
    <h1>Мои закупки</h1>

    <ordersList/>
  </div>
</template>

<script setup>
import ordersList from "@/components/orders-list.vue"
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Comfortaa:wght@400;500;700&family=Lobster&display=swap');

.container {
  width: 1000px;
  max-width: 100%;
  margin: auto;
  padding: 0 15px;
}
h1 {
  text-align: center;
  margin-bottom: 50px;
  font-family: 'Lobster', cursive;
}
body {
  font-family: 'Comfortaa', cursive!important;
  padding-top: 25px!important;
  background: #42275a!important; /* fallback for old browsers */
  background: -webkit-linear-gradient(to right, #42275a, #734b6d)!important; /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to right, #42275a, #734b6d)!important; /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}
.purchase-list {
  padding: 0px;
  list-style: none;

  &__item {
    border-radius: 25px;
    background: #fff;
    color: #000;
    overflow: hidden;
    margin-bottom: 20px;
    padding: 0 25px;

    &__content {
      display: none;
    }

    &-open {
      .purchase-list {
        &__item {
          &__content {
            display: block;
          }
        }
      }
    }

    &__title {
      font-size: 18px;
      padding: 12px;
      text-align: center;
      display: block;
      position: relative;
      cursor: pointer;

      &:hover {
        background: lighten(#000, 90%);

        .icon {
          transform: rotate(180deg);
        }
      }

      .icon {
        width: 30px;
        height: 30px;
        position: absolute;
        right: 15px;
        top: 10px;
        transition: all .25s ease;
      }
    }
  }
}
.products-table {
  width: 100%;
  font-size: 14px;
  margin-top: 15px;
  border-top: 1px lighten(#000, 80%) solid;
  padding-top: 15px;
  margin-bottom: 15px;

  td, th {
    border-bottom: 1px solid black;
    border-collapse: collapse;
    padding: 5px;
  }

  th {
    font-weight: bold!important;
  }

  tr {
    &:hover {
      background: rgba(46, 188, 25, .2);
    }

    &:last-child {
      td {
        border: 0px;
      }
    }
  }

  input {
    border: 0px;
    background: none;
    outline: none!important;
    font-size: 14px;
  }

  select {
    min-width: 100px;
    border: 0px;
    background: none;
    outline: none!important;
  }
}
</style>