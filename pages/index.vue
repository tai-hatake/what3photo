<template>
  <section class="container">
    <div>
      <h1 class="title">
        what3photo
      </h1>
      <h2 class="subtitle">
        住所を入力してください！
      </h2>
      <h2 class="subtitle">
        あなたの住んでいる場所を3つの写真で表します！
      </h2>
      <div class="address-input">
        <el-input placeholder="例）東京都港区芝公園４丁目２−８" v-model="address" class="input-with-select">
          <el-button slot="append" icon="el-icon-search" @click="fetchLatLng"></el-button>
        </el-input>
      </div>
      <div class="caution">※住所は保存しておりませんのでご安心ください。</div>
      <el-row class="photos">
        <el-col :span="6"  v-for="(url, i) in urls" :key=i :offset="i">
          <el-card :body-style="{ padding: '0px' }">
            <img :src="url" class="image">
            <div style="padding: 8px;">
              <span>{{ words[i] }}</span>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>
  </section>
</template>

<script>
import AppLogo from '~/components/AppLogo.vue'
import axios from 'axios'
import { config } from '~/config/config'

export default {
  components: {
    AppLogo
  },
  data () {
    return {
      address: '',
      lat: '',
      lng: '',
      words: [],
      urls: []
    }
  },
  methods: {
    searchPhotos (search_word) {
      axios.get(`${config.GOOGLE.SEARCH.BASE_URL}?key=${config.GOOGLE.SEARCH.API_KEY}&cx=${config.GOOGLE.SEARCH.ENGINE_ID}&searchType=image&q=${search_word}`)
      .then((res) => {
        // console.log(res)
        // 10個の画像からランダム
        this.urls.push(res.data.items[Math.floor( Math.random() * 10)].link)
      })
    },
    fetch3Word () {
      axios.get(`${config.WHAT3WORDS.BASE_URL}?coords=${this.lat},${this.lng}&key=${config.WHAT3WORDS.API_KEY}&lang=ja&format=json&display=full`)
      .then((res) => {
        // console.log(res)
        this.words = res.data.words.split('・')
        for (let word of this.words) {
          this.searchPhotos(word)
        }
      })
    },
    fetchLatLng () {
      axios.get(`${config.GOOGLE.MAPS.BASE_URL}?address=${this.address}&key=${config.GOOGLE.MAPS.API_KEY}`)
      .then((res) => {
          // // response 処理
          // console.log(`res:${JSON.stringify(res)}`)
          this.lat = res.data.results[0].geometry.location.lat
          this.lng = res.data.results[0].geometry.location.lng
          this.fetch3Word()
        })
        .catch(err => {
          console.dir(err)
        })
    }
  }
}
</script>

<style>
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; /* 1 */
  display: block;
  font-weight: 300;
  font-size: 3em;
  color: #e6323e;
  letter-spacing: 2px;
}

.subtitle {
  font-weight: 300;
  font-size: 1.3em;
  color: #526488;
  word-spacing: 5px;
  margin: 12px;
}

.address-input {
  margin: 0 4px;
}

.caution {
  font-size: .8em;
  margin: 4px;
}

.photos {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 16px;
}

.links {
  padding-top: 15px;
}

.image {
  width: 100%;
}
</style>

