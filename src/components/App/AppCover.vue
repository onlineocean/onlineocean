<template>
  <div class="cover">
    <div class="container">
      <div class="cover__inner">
        <div class="cover__info">
          <p class="cover__desc">
            {{ data.descr }}
          </p>
          <h1 class="cover__title">
            {{ data.title }}
          </h1>
          <app-button
          :title='data.btnName'
          link
          :href='data.btnHref'
          ></app-button>
        </div>
        <div class="cover__imgwrapper--dark"
        v-if='theme === "dark"'
        >
          <img src="@/assets/images/cover-graphic-dark.jpg" alt="">
        </div>
        <div class="cover__imgwrapper"
        v-else
        >
          <img src="@/assets/images/cover-graphic.png" alt="">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AppButton from '@/components/App/AppButton.vue';

import { mapGetters } from 'vuex'

export default {
  data() {
    return {
      data:{
        descr: 'The Larget NFT Marketplace',
        title: 'Discover & Collect rate Digital Artwork',
        btnName: 'Explore',
        btnHref: '/cataloge',
      }
    }
  },
  computed: {
    ...mapGetters(['theme']),
  },
  components: {
    AppButton,
  },
}
</script>