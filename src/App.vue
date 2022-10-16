<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import { ref } from 'vue'

import raadingTask1 from './assets/reading.json'
import raadingTask2 from './assets/reading2.json'
import listenTask3 from './assets/listen.json' 

const tabPosition = ref('tab1')
</script>

<template>
  <header>
    <div>
      <div style="display: flex;justify-content: center;margin-bottom: 10px;">
        <img alt="逢考必过" class="logo" src="./assets/fkbg.png" height="150" />
      </div>

      <!-- <div class="wrapper">
        <HelloWorld msg="逢考必过！" />
      </div> -->
    </div>
    
  </header>

  <main>
    <div style="width: 100%;text-align: center;">
      <el-radio-group v-model="tabPosition" style="margin-bottom: 10px;">
        <el-radio-button label="tab1">阅读task1</el-radio-button>
        <el-radio-button label="tab2">阅读task2</el-radio-button>
        <el-radio-button label="tab3">听力task3</el-radio-button>
      </el-radio-group>
    </div>
    

    <TheWelcome v-if="tabPosition == 'tab1'" :dataParam="raadingTask1" />
    <TheWelcome v-else-if="tabPosition == 'tab2'" :dataParam="raadingTask2" />
    <TheWelcome v-else="tabPosition == 'tab3'" :dataParam="listenTask3" />
  </main>
  <footer>
    <div style="display: flex;justify-content: center;font-size: small;">©胡江浩 张星宇 黄琳棠</div>
  </footer>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 0;
}

@media (min-width: 1024px) {
  header {
    /* display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2); */
    display: flex;
    justify-content: center;
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  /* header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  } */
}
</style>
