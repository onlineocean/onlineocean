<template>

  <router-link
    class="row__slide"
    :to="{ name:'Art', params: { itemId: this.artId, tokenId: this.artId, price: this.formatPrice(price), name: 'nft name', description: 'nft description' }}"
    :class='[likeState]'
  >

    <span class="row__slide-blocker"
    v-if='isActive !== true'
    @click.prevent
    ></span>
    <span class="row__slide-top row-top">
      <span class="row-top__left">
        <p class="row__slide-title">
          {{ custom ? custom.name : content.title }}
        </p>
        <p class="row__slide-owner">{{ pretitle.owner }} {{ custom ? custom.owner : content.owner }}</p>
      </span>
      <app-likes
      :info='content.likes'
      @toggledLike='toggleLike()'
      ></app-likes>
    </span>
    <span class="row__slide-imgwrapper">
      <img class='row__slide-img' :src="custom ? custom.image.src : require('@/assets/images/temp/slide-1.jpg')" alt="" />
      <!-- @click.prevent -->
      <app-button
      :title='content.buyTitle'
      view='art-hover'
      ></app-button>
      <slot></slot>
    </span>
    <span class="row__slide-bottom row-bottom">
      <span class="row-bottom__left">
        <p class="row__slide-bid">
          {{ pretitle.bid }}
        </p>
        <p class="row__slide-price">
          {{ formatPrice(price) }}
        </p>
      </span>
      <app-profile
      :user-id='content.owner'
      view='slider'
      ></app-profile>
    </span>
  </router-link>
</template>

<script>
import axios from 'axios';

import AppProfile from '@/components/App/AppProfile.vue';
import AppLikes from '@/components/App/AppLikes.vue';

// converts yoctoNEAR (10^-24) amount into NEAR
import * as nearAPI from "near-api-js";
const { utils } = nearAPI;

export default {
  props: {
    artId:{
      type: Number,
      default: 0,
    },
    custom:{
      type: Object,
      default: null,
    },
    isActive:{
      type: Boolean,
      default: true,
    },
    price: {
      type: Number,
      required: false,
    },
    tokenId: {
      type: String,
      required: false,
    }
  },
  data() {
    return {
      pretitle:{
        owner: 'Owner: ',
        bid: 'Current bid:',
      },
      content: {
        title: 'Abstract 3D work content more than ever',
        owner: 'artstudio',
        bid: '0.034 NEAR',
        buyTitle: 'Buy now',
        likes:{
          count: 131,
          status: false,
        },
        chars:{
          amount: '126',
          probability: '8%',
          rarityLabel: 'Legendary',
        },
      },
      liked: false,
    }
  },
  mounted () {
    this.loadCard()
  },
  methods:{
    formatPrice(price) {
      return `${utils.format.formatNearAmount(price)} NEAR`;
    },
    loadCard(){
      // axios
      //   .get('https://google.com&=artId')
      //   .then(function(response){
      //     this.content = response
      //   })
    },
    toggleFavourite(){
      this.liked = !this.liked
    },
    toggleLike(){
      // Убрать
      if(this.content.likes.status === false){
        this.content.likes.count++
      }else{
        this.content.likes.count--
      }
      this.content.likes.status = !this.content.likes.status
      axios.post('/favourite', this.artId)
        .then(function () {
          // Uncomment 
          // if(this.content.likes.status === false){
          //   this.content.likes.count++
          // }else{
          //   this.content.likes.count--
          // }
          // this.content.likes.status = !this.content.likes.status
        })
    },
  },
  computed: {
    likeState() {
      return this.liked ? "row__slide--active" : ""
    },
  },
  components: {
    AppProfile,
    AppLikes,
  },
};
</script>