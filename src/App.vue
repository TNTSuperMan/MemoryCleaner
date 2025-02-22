<script setup lang="ts">
import { ref, watch } from 'vue';

const enum State{
  Home,
  Scan,
  Clean,
  Complete
}
const state = ref(State.Home);
const progress = ref(0);
const volume = ref(0);
watch(state,()=>{
  if(state.value == State.Scan){
    progress.value = 0;
    volume.value = Math.random() * 100 + 100; //スキャンする量を取得()
    const interval = setInterval(()=>{
      progress.value += Math.random()*2; //スキャン()
      if(progress.value > volume.value){ //スキャンが完了したらクリーンアップ
        clearInterval(interval);
        volume.value = progress.value; //ずれ補正
        setTimeout(()=>state.value = State.Clean, 1000)
      }
    },100)
  }else if(state.value == State.Clean){
    progress.value = 0; // クリーン状況を表示するためリセット
    const interval = setInterval(()=>{
      progress.value += Math.random(); //クリーンアップ処理()
      if(progress.value > volume.value){ //クリーンアップが終わったら終了画面に
        clearInterval(interval);
        state.value = State.Complete;
      }
    },100)
  }
})
// ローディングのやつの処理
const loader = ref("oooo");
let lv = 0;
const l = [ // ローディングテキストを配列で管理
  "oooooo",
  "0ooooo",
  "o0oooo",
  "oo0ooo",
  "ooo0oo",
  "oooo0o",
  "ooooo0",
]
setInterval(()=>{
  lv++;
  loader.value = l[lv%l.length];
  lv %= l.length;
},100)
</script>

<template>
  <template v-if="state==State.Home" class="home">
    <h1>メモリクリーナー</h1>
    <p>デバイスのメモリをクリーンアップします</p>
    <button @click="state=State.Scan">クリーンアップ</button>
  </template>
  <div v-else-if="state==State.Scan" class="scan">
    <h1>スキャン中...</h1>
    <span class="loader">{{ loader }}</span><br>
    <span class="volume">{{ Math.round(scanValue) }}MB</span>
  </div>
  <div v-else-if="state==State.Clean" class="scan">
    <h1>クリーンアップ中...</h1>
    <span class="loader">{{ loader }}</span><br>
    <span class="volume">{{ Math.round(scanValue) }}MB / {{ Math.round(volume) }}MB</span>
  </div>
  <div v-else class="complete">
    <h1>クリーンアップが完了しました</h1>
    <p>あなたは安心してデバイスを使えます</p>
  </div>
</template>

<style scoped>
h1{
  padding: 15px;
}
.home h1{
  background:skyblue;
  margin: 0;
  outline: 0;
}
button{
  font-size: 20px;
  padding: 10px;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  background: springgreen;
  transition: all 0.5s ease;
}
button:hover{
  background: greenyellow;
}
div{
  background: springgreen;
  position: fixed;
  left: 0;
  top:  0;
  width: 100%;
  height: 100%;
  margin: 0;
  display:block;
}
.complete{
  background: greenyellow;
}
.volume{
  font-size: 40px;
}
.loader{
  font-family: monospace;
  white-space:pre;
}
</style>
