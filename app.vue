<template>
 <div class="base">
    <div class="q-area">
      <h1>{{ question }}</h1>
    </div>
    <div class="a-area">
      <h3 class="score">{{ score }}</h3>
      <div class="buttons">
        <v-btn
          v-for="type in types"
          class="btn"
          @click="judge(type)"
          :disabled="gameover"
        >
          {{ type }}
        </v-btn>
      </div>
    </div>
    <div class="other">
      <p v-if="gameover" class="comment">{{ comment }}</p>
      <v-btn v-if="gameover" class="retry-btn" @click="retry">retry</v-btn>
      <div v-if="highscore" class="highscore">highscore: {{ highscore }}</div>
    </div>
 </div>
</template>

<script setup>
import zyodoshiDict from "./assets/zyodoshis.json"
import { useStorage } from "@vueuse/core"

const question = ref(null);
const answer = ref(null);
const score = ref(0);
const gameover = ref(false);
const comment = ref("");

const highscore = useStorage('highscore', 0);

const types = ref(Object.keys(zyodoshiDict));

const init = () => {
  score.value = 0;
  makeQ();
}

const makeQ = () => {
  const a = types.value[Math.floor(Math.random() * types.value.length)];
  const q = zyodoshiDict[a][Math.floor(Math.random() * zyodoshiDict[a].length)];
  
  if (q != question.value) {
    question.value = q;
    answer.value = a;
  } else {
    makeQ();
  }
}

const judge = (ans) => {
  const isCorrect = zyodoshiDict[ans].includes(question.value);

  if (isCorrect) {
    score.value++;
    highscore.value = Math.max(score.value, highscore.value)
    makeQ();
  } else {
    gameover.value = true;
    comment.value = `"${question.value}" : "${answer.value}"`;
  }
}

const retry = () => {
  gameover.value = false;
  init()
}

init();
</script>

<style scoped>
.base {
  margin: 0 auto;
  padding: 0 200px;
  padding-bottom: 150px;
  width: 100vw;
  min-width: 100vw;
  min-height: 100vh;
  justify-content: center;
  align-items: center;
  text-align: center;
}
/* 縦画面用 */
@media screen and (max-width: 960px) {
  .base {
    padding: 0 50px;
  }
}
.q-area {
  text-align: center;
  width: 100%;
  margin: 100px auto;
}
.a-area {
  width: 500px;
  margin: 0 auto;
}
.score {
  margin-bottom: 20px;
}
.buttons {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
.btn {
  margin: 2px;
  width: 120px;
  height: 50px;
}
.comment {
  margin-top: 10px;
  color: red;
}
.retry-btn {
  margin-top: 20px;
}
.highscore {
  margin-top: 10px;
}
</style>
