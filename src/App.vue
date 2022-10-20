<script setup>
import QuestionList from './views/QuestionList.vue'
import { ref, reactive, onMounted } from 'vue'
import axios from 'axios'

import questions from './assets/data/questions2.json' 
import demo from './assets/data/demo.json' 

const tabPosition = ref('')
const flag = ref(true)
const dialogVisible = ref(false)
const jsonUrl = ref('')
const loadFlag = ref(false)
const demoJson = ref(JSON.stringify(demo,censor,4))

const data = reactive({
  questionsData: reactive(questions)
})

function censor(key,value){
    if(typeof(value) == 'function'){
         return Function.prototype.toString.call(value)
    }
    return value;
}

function requestJson(url) {
  loadFlag.value = true
  flag.value = false
  axios.get(url).then((res) => {
    console.log('res.data = ', res.data)
    data.questionsData = reactive(res.data)
    if (data.questionsData.length > 0) {
      tabPosition.value = data.questionsData[0].id
    }
    loadFlag.value = false
    flag.value = true
  })
  .catch(()=>{
    loadFlag.value = false
    ElMessage({
    message: '链接访问异常',
    type: 'warning',
  })
  })
  
}

function confirmDialog() {
  if (jsonUrl.value){
    requestJson(jsonUrl.value)
  }
  dialogVisible.value = false
}

onMounted(() => {
  if (data.questionsData.length > 0) {
    tabPosition.value = data.questionsData[0].id
  }
})
</script>

<template>
  
  <header>
    <div>
      <div style="display: flex;justify-content: center;margin-bottom: 10px;">
        <a href="https://www.makerhu.com/"><img alt="逢考必过" class="logo" src="./assets/fkbg.png" height="150" /></a>
      </div>
    </div>
  </header>

  <main v-loading="loadFlag">
    <div style="width: 100%;text-align: center;">
      <el-radio-group v-model="tabPosition" style="margin-bottom: 10px;">
        <el-radio-button :label="item.id" v-for="item in data.questionsData">{{ item.name }}</el-radio-button>
      </el-radio-group>
    </div>
    
    <div v-if="flag" v-for="item in data.questionsData">
      <QuestionList v-if="item.id == tabPosition" :dataParam="item.questions" @showDialog="(v)=> dialogVisible = v" />
    </div>
    
  </main>
  <footer>
    <div style="display: flex;justify-content: center;font-size: small;margin-top: 20px;">©胡江浩 张星宇 黄琳棠</div>
  </footer>

  <el-backtop :right="30" :bottom="30" style="width: 32px;height: 32px;" />
  <el-dialog
    v-model="dialogVisible"
    title="自定义题目"
    width="350px"
  >
    <el-input v-model="jsonUrl" placeholder="Json文件链接" />
    <div style="margin:10px 0 5px 0">Json格式参考</div>
    <el-input
      v-model="demoJson"
      type="textarea"
      :rows="10"
      placeholder="Please input"
    />

    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="confirmDialog()"
          >确定</el-button
        >
      </span>
    </template>
  </el-dialog>
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
    display: flex;
    justify-content: center;
  }

  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
