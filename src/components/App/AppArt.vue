<template>
  <router-link class="art" :to='artLink'
  :class='[artSize, artRarity, artDate]'
  >
    <slot></slot>
    <span class="art__imgwrapper">
      <img :src="art.cover.src" alt="">
      <!-- @click.prevent -->
      <app-button
      :title='buyTitle'
      view='art-hover'
      ></app-button>
      <span class="art__price">
        {{ art.price }}
      </span>
    </span>
    <span class="art__info">
      <app-profile
      v-if='!short'
      :user-id='art.artstudio'
      :contentSlot='art.descr'
      view='hasDescr'
      ></app-profile>
      <span class="art__descr"
      v-else
      >
        {{ art.descr }}
      </span>
    </span>
  </router-link>
</template>

<script>
import AppProfile from '@/components/App/AppProfile.vue';

import moment from 'moment'
import axios from 'axios'

export default {
  props: {
    artId: {
      type: Number,
      required: true, 
    },
    short:{
      type: Boolean,
      default: false,
    },
    hasSlot:{
      type: Boolean,
      default: false
    }
  },
  mounted () {
    this.loadArt()
  },
  data() {
    return {
      charsView: false,
      todayDate: moment(),
      buyTitle: 'Buy now',
      art:{
        price: '20.034 NEAR',
        descr: 'Abstract 3D Content Art fdfds ee rwerew',
        owner: 'artstudio',
        dateOfCreate: '2021-10-21T22:53:30',
        rarity: 'common', // common, epic, rare, legendary
        cover:{
          src: require('@/assets/images/temp/art-1.jpg')
        },
        video:{
          src: '',
        },
        chars:{
          amount: '126',
          probability: '8%',
          rarityLabel: 'Legendary',
        },
      }
    }
  },
  methods: {
    loadArt(){
      axios
        .get('art/' + this.artId)
        .then(function(response){
          this.art = response
        })
    },
  },
  computed: {
    artLink(){
      return '/art/' + this.artId
    },
    artSize(){
      return this.short ? 'art--short' : ''
    },
    artRarity(){
      return 'art--' + this.art.rarity
    },
    artDate(){
      let difference = this.todayDate.diff(moment(this.art.dateOfCreate), 'days')
      return difference <= 14 ? 'fire' : ''
    },
  },
  components: {
    AppProfile,
  },
}
</script>