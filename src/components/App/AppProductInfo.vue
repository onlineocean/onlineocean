<template>
  <div class="product-info">
    <div class="container">
      <div class="product-info__inner">
        <div class="product-info__coverwrapper">
          <video class='product-info__video' src="@/assets/videos/ex-1.mp4"
          muted loop
          ref='productVideo'
          v-if="art.video.src"
          ></video>
          <div class="product-info__imgwrapper"
          v-else
          >
            <img :src="art.cover.src" alt="">
          </div>
          <button class="product-info__play btn-clear"
          @click='toggleVideo()'
          v-if="art.video.src"
          >
            <svg width="22" height="24" viewBox="0 0 22 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M4.5766 1.07545C2.5159 -0.106593 0.845215 0.861755 0.845215 3.23659V20.7618C0.845215 23.139 2.5159 24.1061 4.5766 22.9252L19.8945 14.1405C21.9559 12.958 21.9559 11.0422 19.8945 9.86004L4.5766 1.07545Z" fill="#EA4C89"/>
            </svg>
          </button>
          <app-profile
          userId='22'
          view='product-info'
          ></app-profile>
          <app-likes
          view='big'
          :info='art.likes'
          @toggledLike='toggleLike()'
          ></app-likes>
        </div>
        <div class="product-info__content">
          <div class="dots">
            <button class="btn-clear"
            @click='toggleShare()'
            >
              <svg width="25" height="6" viewBox="0 0 25 6" fill="none" xmlns="http://www.w3.org/2000/svg">
                <circle cx="22" cy="3" r="3" transform="rotate(-90 22 3)" fill="#C0C0C0"/>
                <circle cx="13" cy="3" r="3" transform="rotate(-90 13 3)" fill="#C0C0C0"/>
                <circle cx="3" cy="3" r="3" transform="rotate(-90 3 3)" fill="#C0C0C0"/>
              </svg>
            </button>
            <ul class="share dots__modal"
            :class='shareActive'
            >
              <li class="share__item">
                <a class="share__link" :href="'https://twitter.com/intent/tweet?text=' + shareLinkHref">
                  <span class="share__imgwrapper">
                    <img src="https://www.pngrepo.com/png/5125/180/avatar.png" alt="">
                  </span>
                  Twitter
                </a>
              </li>
              <li class="share__item">
                <a class="share__link" :href="'https://telegram.me/share/?&text=' + shareLinkHref">
                  <span class="share__imgwrapper">
                    <img src="https://www.pngrepo.com/png/5125/180/avatar.png" alt="">
                  </span>
                  Telegram
                </a>
              </li>
            </ul>
          </div>
          <h1 class="product-info__name">
            {{ productName ?? 'name '}}
          </h1>
          <p class="product-info__descr">
            {{ descr }}
          </p>
          <div class="product-info__middle">
            <div class="product-info__price">
              <p class="product-info__subtitle">
                Current bid:
              </p>
              <p class="product-info__bid">
                {{ price }}
              </p>
              <!-- <p class="product-info__cost">
                $ 48.85 //TODO: convert to local currency
              </p> -->
            </div>
            <div class="product-info__auction auction"
            v-show='auctionIsActive'
            >
              <p class="auction__text product-info__subtitle">
                Auction ending in
              </p>
              <vue-countdown
              :time='difference'
              v-slot="{ days, hours, minutes, seconds }"
              @end='onTimerEnd'
              >
                <ul class="auction__items">
                  <li class="auction__item">
                    <p class="auction__numb">
                      {{ (days * 24) + hours }}
                    </p>
                    <p class="auction__time">
                      Hours
                    </p>
                  </li>
                  <li class="auction__item">
                    <p class="auction__numb">
                      {{ minutes }}
                    </p>
                    <p class="auction__time">
                      Minutes
                    </p>
                  </li>
                  <li class="auction__item">
                    <p class="auction__numb">
                      {{ seconds }}
                    </p>
                    <p class="auction__time">
                      Seconds
                    </p>
                  </li>
                </ul>
              </vue-countdown>
            </div>
          </div>
          <div class="product-info__buttons">
            <app-button
            v-if="box"
            title='Open'
            view='purple'
            big
            link
            :href='boxOpenLink'
            ></app-button>
            <!-- <app-button
            title='Sell'
            view='yellow'
            big
            ></app-button> -->
            <app-button
            title='Buy'
            view='green'
            big
            @click="buy"
            ></app-button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueCountdown from '@chenfengyuan/vue-countdown';

import AppProfile from '@/components/App/AppProfile.vue';
import AppLikes from '@/components/App/AppLikes.vue';

import * as nearAPI from "near-api-js";
const { utils } = nearAPI;

export default {
  props: {
    name: {
      type: String,
      required: true, 
    },
    descr: {
      type: String,
      required: true, 
    },
    box:{
      type: Boolean,
      default: false,
    },
    tokenId: {
      type: String,
      required: true, 
    },
    price: {
      type: String,
      required: true, 
    },
  },
  data() {
    return {
      shareStatus: false,
      shareText: 'Cool%20art%20here%20',
      currPath: document.URL,
      endAuction: '2021-12-12T22:53:30',
      dateNow: 0,
      difference: 0,

      auctionIsActive: true,
      videoActive: false,
      likedBox: null,
      info:{
      },
      art:{
        price: '20.034 NEAR',
        descr: 'Abstract 3D Content Art fdfds ee rwerew',
        owner: 'artstudio',
        dateOfCreate: '2021-10-21T22:53:30',
        rarity: 'common', // common, epic, rare, legendary
        cover:{
          src: require('@/assets/images/temp/slide-1.jpg')
        },
        video:{
          src: '',
        },
        chars:{
          amount: '126',
          probability: '8%',
          rarityLabel: 'Legendary',
        },
        likes:{
          count: 121,
          status: true,
        },
      }
    }
  },
  created () {
    this.dateNow = new Date()
    this.dateNeed = new Date(this.endAuction)
    this.difference = this.dateNeed - this.dateNow
  },
  computed: {
    productName() {
      const currId = this.$route.params.itemId
      let rightID = currId
      if (currId.length < 3){
        const needZero = 3 - currId.length
        rightID = '0'.repeat(needZero) + currId 
      }
      const stringWithId = this.name.replace(/id/i, '#' + rightID)
      return stringWithId
    },
    boxOpenLink(){
      return this.$route.params.itemId + '/open/'
    },
    shareActive(){
      return this.shareStatus ? 'share--active' : ''
    },
    shareLinkHref(){
      return this.shareText + this.currPath
    },
    yoktoNearPrice() {
      return this.price.replace('NEAR', '');
    }
  },
  methods: {
    async buy() {
      const { connect, keyStores, WalletConnection } = nearAPI;

      const config = {
        networkId: "testnet",
        keyStore: new keyStores.BrowserLocalStorageKeyStore(),
        nodeUrl: "https://rpc.testnet.near.org",
        walletUrl: "https://wallet.testnet.near.org",
        helperUrl: "https://helper.testnet.near.org",
        explorerUrl: "https://explorer.testnet.near.org",
      };

      // // connect to NEAR
      const near = await connect(config);

      // // create wallet connection
      const wallet = new WalletConnection(near);

      // const missingAccount = JSON.parse(localStorage.getItem('undefined_wallet_auth_key'));
      // const account = wallet.account(missingAccount.accountId);
      const account = wallet.account();

     // console.log('buy account: %o', account);

      //load the contract //should this be load contract?!
      const contract = new nearAPI.Contract(
        account, // the account object that is connecting
        "dev-1642413213650-29062548325851",
        {
          // name of contract you're connecting to
          viewMethods: ["get_market", "check_access", "get_token_owner"], // view methods do not change state but usually return a value
          changeMethods: ["buy"], // change methods modify state
          sender: account.accountId, // account object to initialize and sign transactions.
        }
      );



// console.log('accountId: %o', missingAccount.accountId);
//       const contract = await near.loadContract(
//         "dev-1642413213650-29062548325851",
//         {
//           // name of contract you're connecting to
//           viewMethods: ["get_market", "check_access", "get_token_owner"], // view methods do not change state but usually return a value
//           changeMethods: ["buy"], // change methods modify state
//           sender: missingAccount.accountId, // account object to initialize and sign transactions.
//         }
//       );


      const response = await contract.buy(
        {
          "token_id": this.tokenId
        },
        300000000000000, //attached gas
        utils.format.parseNearAmount(this.yoktoNearPrice)  //attach deposit
      );

      console.log('response: %o', response);

    },
    onTimerEnd() {
      this.auctionIsActive = false
    },
    toggleVideo(){
      if (this.videoActive === false){
        this.$refs.productVideo.play()
        this.videoActive = true
      } else{
        this.$refs.productVideo.pause()
        this.videoActive = false
      }
    },
    toggleLike(){
      // Убрать
      if(this.art.likes.status === false){
        this.art.likes.count++
      }else{
        this.art.likes.count--
      }
      this.art.likes.status = !this.art.likes.status
      // Раскоментировать
      // axios.post('/favourite', this.$route.params.itemId)
      //   .then(function (response) {
      //     if(this.art.likes.status === false){
      //       this.art.likes.count++
      //     }else{
      //       this.art.likes.count--
      //     }
      //       this.art.likes.status = !this.art.likes.status
      //   })
    },
    toggleShare(){
      this.shareStatus = !this.shareStatus
    },
  },
  
  components: {
    VueCountdown,
    AppProfile,
    AppLikes
  },
}
</script>