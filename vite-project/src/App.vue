<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'

import * as Y from 'yjs'
import { WebsocketProvider } from 'y-websocket'
import { onBeforeMount, onUnmounted, ref } from 'vue';

const getQueryParam = (paramName:string) => {
  const queryString = window.location.search;
  const urlParams = new URLSearchParams(queryString);
  return urlParams.get(paramName);
}

const clientName = getQueryParam('clientName') || 'NoName'
const doc = new Y.Doc()
const docId = doc.clientID.toLocaleString()

const status = ref('')
const targetArr = ref<any[]>([])
const targetMap = ref({})
const arr = doc.getArray('testArray')
arr.observe((arg)=>{
  console.warn('updated ', arg)
  targetArr.value = arg.target.toJSON()
})
const map = doc.getMap('testMap')
map.observe((arg)=>{
  console.warn('updated ', arg)
  targetMap.value = arg.target.toJSON()
})

const addArray = ()=>{
  const time = new Date().getTime()

  arr.insert(0, [`${docId}-${clientName}-${time}`])
}
const clearArray = ()=>{
  arr.delete(0, arr.length)
}

const addMap = ()=>{
  const time = new Date().getTime()

  map.set('mapKey2', [`${docId}-${clientName}-${time}`])
}
const clearMap = ()=>{
  map.delete('mapKey2')
}

onBeforeMount(async()=>{
  await new Promise((res)=>setTimeout(res, 10000))
const wsProvider = new WebsocketProvider('ws://localhost:1234', 'my-roomname', doc)
wsProvider.on('status', (event: any) => {
  console.log(event.status) // logs "connected" or "disconnected"
  status.value = event.status
})
onUnmounted(()=>{
    wsProvider?.destroy()
})

})
</script>

<template>
  <div>
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>
  <div>
    <p>{{docId}}</p>
    <p>{{status || 'No'}}</p>
    <p>{{clientName}}</p>
    <!-- <p><pre>{{JSON.stringify(targetArr, null, 2)}}</pre></p> -->
    <p><pre>{{JSON.stringify(targetMap, null, 2)}}</pre></p>
    AAAAAAAAAAAAAAAA
    <!-- <button @click="addArray">AddArr</button> -->
    <!-- <button @click="clearArray">ClearArr</button> -->
    <button @click="addMap">AddMap</button>
    <button @click="clearMap">ClearMap</button>
  </div>

  <HelloWorld msg="Vite + Vue" />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
