<script setup lang="ts">
import {ref, onMounted } from 'vue';

const textTrain = ref('');
const textLn = ref(0);
const textTrainTest = ref('');
const wrongCounter = ref(0);
const keyListener = ref('on');

const newText = async () =>  {

  document.addEventListener('keydown', handleKeyDown);
  const response = await fetch('https://fish-text.ru/get?format=html&number=2');
  textTrain.value = await response.text();
  textTrain.value = textTrain.value.replace('<p>', '');
  textTrain.value = textTrain.value.replace('</p>', '');
  for (let i = 0; i < textTrain.value.length; i++) {
    const char = '<span style = "color: black">' + textTrain.value[i] + '</span>';
    textTrainTest.value+=char;
  }
  textLn.value = textTrain.value.length;
};

onMounted(newText);

const counterSec = ref(0);
const timerIs = ref('off');
let intervalId = null;

const StartTimer = () => {
  if (timerIs.value === 'off')
  {
    timerIs.value = 'on'
    intervalId = setInterval(() => {
    counterSec.value++;
  }, 1000);
  }
};

const ReloadTrain = () => {
  if (timerIs.value === 'on') {
    clearInterval(intervalId);
    timerIs.value = 'off';
  }
  counterSec.value = 0;
  textTrainTest.value = "";
  wrongCounter.value = 0;
  newText();
};

const pressKey = ref(0);

const handleKeyDown = (event: any) => {
      if (textTrain.value.length > 0)
      {
        StartTimer();
        if (event.key == 'Shift' || event.key== 'Control' || event.key== 'Alt'|| event.key== 'CapsLock')
        {
          return;
        }
        else
        {
          console.log(event.key);
          console.log(textTrain.value[0]);
          console.log(textTrainTest.value);
          if (event.key === textTrain.value[0])
          {
            event.preventDefault();
            textTrainTest.value = textTrainTest.value.replace('black', 'green');
          }
          else{
            event.preventDefault();
            textTrainTest.value = textTrainTest.value.replace('black', 'red');
            wrongCounter.value++;
          }
          textTrain.value = textTrain.value.substring(1);
        }
      }
      else{
        if (timerIs.value === 'on') {
      clearInterval(intervalId);
      timerIs.value = 'off';
  }
      }
  }

</script>

<template>
  <div class="train-board" @keydown="handleKeyDown, StartTimer">
    <h1>Text Trainer</h1>
    <h2>{{ Math.floor(counterSec/60).toString().padStart(2, '0') }}:{{ (counterSec%60).toString().padStart(2, '0') }} </h2>
    <h2> mistakes: {{ wrongCounter }}</h2>
    <div class="text-back" id="textTrain" v-html="textTrainTest"></div>
    <button @click="ReloadTrain" class="reload">Reload</button>
  </div>
</template>



<style scoped>

.reload{
  margin: 20px;
  border-color: black;
}
.train-board
{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  height: 100vh;
  margin: 0%;
  padding: 0%;
}

.train-board h1
{
  margin: 0%;
}
.train-board h2
{
  margin: 0%;
}

.text-back
{
  background-color: gainsboro;
  border-radius: 20px;
  padding: 20px;
  font-weight: 500;
  font-size: 30px;
  width: 75%;

}
.text-inp
{
  margin: 20px;
  padding: 20px;
  font-weight: 500;
  vertical-align: top;
  white-space: normal;
  border-radius: 20px;
  width: 75%;
  height: 10%;
}

</style>
