<script setup>
import { ref, reactive,onMounted,onUnmounted } from 'vue'
import { Refresh, Top, Hide, View } from '@element-plus/icons-vue'

const props = defineProps({
  dataParam: {
    type: Array,
    required: true
  }
})

let data1 = reactive(props.dataParam)
function onSelect(selectItem) {
  selectItem.selected = !selectItem.selected
}
function onRestart() {
  data1 = reactive(shuffleSelf(data1))
  data1.forEach(element => {
    element.choice = reactive(shuffleSelf(element.choice))
    element.choice.forEach(item => {
      item.selected = false
    })
  });
}
const isOpenEyes = ref(false)
function openEyes() {
  isOpenEyes.value = !isOpenEyes.value
  data1.forEach(element => {
    element.choice.forEach(item => {
      item.selected = item.correct && isOpenEyes.value ? true : false
    })
  });
}

function shuffleSelf(array, size) {
    var index = -1,
        length = array.length,
        lastIndex = length - 1;

    size = size === undefined ? length : size;
    while (++index < size) {
        var rand = index + Math.floor( Math.random() * (lastIndex - index + 1)),
            value = array[rand];

        array[rand] = array[index];

        array[index] = value;
    }
    array.length = size;
    return array;
}

const backTopFlag = ref(false)//用来判断样式
const backTop = () => {
  let top = document.documentElement.scrollTop//获取点击时页面的滚动条纵坐标位置
  const timeTop = setInterval(() => {
    document.documentElement.scrollTop = top -= 100//一次减50往上滑动
    if (top <= 0) {
      clearInterval(timeTop)
    }
  }, 5)//定时调用函数使其更顺滑
}
const handleScroll = () => {
  if (document.documentElement.scrollTop > 20) {
    backTopFlag.value = true
  } else {
    backTopFlag.value = false
  }
  //往下滑超过20则显示 否则则不显示按钮
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})//监听滚动事件

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})//离开页面时移除监听事件

</script>

<template>
  <div style="width: 100%;text-align: right;position: sticky;top: 10px;z-index: 999;">
    <el-button circle :icon="Refresh" @click="onRestart()"></el-button>
    <el-button circle :icon="isOpenEyes? View:Hide" @click="openEyes()" style="margin: 0 5px 0 5px;"></el-button>
  </div>
  
  <div v-show="backTopFlag" style="width: 100%;text-align: right;position: fixed;bottom: 20px;z-index: 999;">
    <el-button circle :icon="Top" @click="backTop()" style="margin-right: 38px;"></el-button>
  </div>
  
  <el-card class="box-card" v-for="item,index in data1">
    <template #header>
      <div class="card-header">
        <span>{{ (index+1) + '. ' + item.question }}</span>
      </div>
    </template>
    <div v-for="o in item.choice" :key="o.key">
      <el-button class="answer-btn" :type="o.selected ? (o.correct ? 'success':'danger'):''" :text="!o.selected" @click="onSelect(o)">
        {{ o.content + (o.selected && o.reason ? (' — '+o.reason):'') }}
      </el-button>
    </div>
  </el-card>
</template>

<style>
.box-card {
  margin-top: 10px;
}

.answer-btn {
  margin-top: 3px;
  width: 100%;
  word-wrap:break-word;
  overflow-wrap: break-word;
  white-space: normal;
  height: auto;
  overflow: hidden;
  text-align: left;
  justify-content: left;
}
</style>