<template>
  <div class="create-modal__chars create-chars">
    <div class="create-chars__row"
    v-for='row in loaded'
    :key='row'
    >
      <div class="create-chars__col create-chars__name">
        <p class="create-chars__name-text">
          {{ row.name }}
        </p>
      </div>
      <div class="create-chars__col create-chars__amount">
        <app-input
        v-if='!row.first'
        :inputDefaultValue='row.amount'
        type='number'
        ></app-input>
        {{ row.first ? row.amount : ''}}
      </div>
      <div class="create-chars__col create-chars__probability">
        <span
        v-if='row.first'
        >
          {{ row.probability }}
        </span>
        <slider
        v-else
        v-model='row.probability'
        :min='0'
        :max='100'
        :format='format'
        ></slider>
      </div>
      <div class="create-chars__col create-chars__price">
        <p class="create-chars__price-text">
          {{ row.price }}
        </p>
      </div>
      <div class="create-chars__col create-chars__watch">
        <span
        v-if='row.first'
        >
          {{ row.href }}
        </span>
        <router-link 
        :to='row.href'
        v-else
        >
          <img src="@/assets/images/watch.svg" alt="">
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import Slider from '@vueform/slider'
import AppInput from '@/components/App/AppInput.vue';

import '@vueform/slider/themes/default.css'

export default {
  data() {
    return {
      probabilityValue: 0,
      format: function (value) {
        return `${Math.round(value)}%`
      },
      loaded:[
        {
          first: true,
          name: 'name',
          amount: 'amount',
          probability: 'probability',
          price: 'price',
          href: 'watch',
        },
        {
          name: 'Book Knight Art 3d kek uo9',
          amount: 335,
          probability: 25,
          price: '20.034 NEAR',
          href: '',
        },
        {
          name: 'Sword',
          amount: 27,
          probability: 60,
          price: '0.0345 NEAR',
          href: '',
        },
        {
          name: 'Sword',
          amount: 257,
          probability: 8,
          price: '0.0345 NEAR',
          href: '',
        },
        {
          name: 'Sword',
          amount: 257,
          probability: 7,
          price: '0.0345 NEAR',
          href: '',
        },
      ]
    }
  },
  components: {
    Slider,
    AppInput,
  },
}
</script>