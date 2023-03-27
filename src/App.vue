<script setup lang="ts">
import { ref } from 'vue'
import { RouterLink, RouterView } from 'vue-router'
import axios from 'axios'
const key = 'yhwl'
const loading = ref(false)
const getUrl = async () => {
  if (loading.value) {
    return
  }
  loading.value = true
  let today = new Date().getDay()
  let _s = JSON.parse(localStorage.getItem(key) || ' {}')
  console.log(_s)

  let num = _s?.num || 0
  let day = _s?.day || ''
  console.log(num, num > 5, day, today, day == today)

  if (num > 5 && day == today) {
    loading.value = false
    return alert('每天最多6次机会，凌晨12：00准时刷新，想要更多机会请咨询小飞机联系')
  }
  open(num)
}
const inputVal = ref('')

const open = async (num: number) => {
  let res = await axios({
    method: 'GET',
    url: `http://hj.viphk.91tunnel.com/api/hjjx?id=${inputVal.value}`
  })
  loading.value = false
  if (res.data.msg === 'success') {
    console.log(res.data.data)
    inputVal.value = ''
    localStorage.setItem(
      key,
      JSON.stringify({
        num: num + 1,
        day: new Date().getDay()
      })
    )
    window.open(res.data.data)
  } else {
    alert(res.data.data)
  }
}
</script>

<template>
  <header>
    <div><input v-model="inputVal" placeholder="输入帖子链接" type="text" /></div>
    <button @click="getUrl">解析{{ loading ? '中。。。' : '' }}</button>
    <button class="my" @click="open(0)">2</button>
  </header>

  <RouterView />
</template>

<style scoped>
input {
  margin-top: 30vh;
  width: 100%;
  height: 40px;
  border: none;
  border-bottom: 0.5px solid gray;
}
button {
  width: 100px;
  height: 40px;
  border-radius: 8px;
  border: none;
  margin-top: 10px;
}
.my {
  position: fixed;
  bottom: 0;
  right: 0;
  background-color: red;
  opacity: 0;
}
</style>
