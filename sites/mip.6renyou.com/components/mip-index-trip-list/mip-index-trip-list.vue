<style scoped>
.text-overflow{
    text-overflow: ellipsis;
    width:300px;
    overflow:hidden;
    white-space: nowrap;
}
</style>

<template>
  <div class="index-trip-list">
    <ul class="index-trip-ul">
      <li
        v-for="(item, index) in list"
        :key="index"
        class="tjtrip-li">
        <a
          :href="item.href"
          mip-link>
          <div class="tjtrip-img">
            <mip-img :src="item.cover" />
            <div class="tjtrip-days">
              <span class="tjtrip-day"><i>{{ item.days }}</i>天</span>
            </div>
          </div>
          <div class="tjtrip-btm">
            <h2 class="tjtrip-title text-overflow">{{ item.title }}</h2>
            <p class="tjtrip-dest">{{ item.tocity_name }}</p>
          </div>
        </a>
      </li>
    </ul>
  </div>

</template>

<script>
import toast from '../../common/toast'

let options = {
  position: 'bottom-center',
  duration: 2000
}

let isLoaded = false
let page = 1
let destName = ''

export default {
  props: {
    'name': {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      list: []
    }
  },
  mounted () {
    destName = this.name
    if (!destName || destName === null || destName === '') {
      destName = '北美|日本|泰国|澳大利亚|加拿大'
    }

    this.load(this.start)
  },
  methods: {
    load (cb) {
      if (isLoaded) return
      fetch('https://m.6renyou.com/mip_service/get_hot_trip_list?dest=' + destName + '&page=' + page).then(resp => resp.json()).then(resp => {
        if (!resp || resp === null || resp.length === 0) {
          toast.show('没有更多', options)
          isLoaded = true
          return
        }
        page++
        resp = resp.map(it => {
          it.href = '/trip/' + it.id
          return it
        })
        this.list = this.list.concat(resp)
        if (cb)setTimeout(() => { cb.call(this) }, 1000)
      }).catch(err => {
        console.log(err.message)
      })
    },
    start () {
      let scrollHeight = MIP.viewport.getScrollHeight() // document.documentElement.scrollHeight > document.body.scrollHeight ? document.documentElement.scrollHeight : document.body.scrollHeight,
      let scrollTop = MIP.viewport.getScrollTop() // document.documentElement.scrollTop > document.body.scrollTop ? document.documentElement.scrollTop : document.body.scrollTop,
      let clientHeight = MIP.viewport.getHeight()// document.documentElement.clientHeight < document.body.clientHeight ? document.documentElement.clientHeight : document.body.clientHeight;

      // console.log(scrollHeight, scrollTop, clientHeight, document.documentElement.clientHeight, document.body.clientHeight);
      if ((scrollHeight === scrollTop + clientHeight) && (scrollTop !== 0)) {
        // console.log('>>>>>>>>>');
        this.load(this.start)
      } else {
        if (!isLoaded) {
          setTimeout(this.start, 500)
        }
      }
    }
  }
}
</script>
