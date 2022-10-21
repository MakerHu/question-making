<script setup>
import { ref, reactive,watch,onMounted,onUnmounted } from 'vue'
import { Refresh, Hide, View, Edit } from '@element-plus/icons-vue'

const emit = defineEmits(['showDialog'])
const btnFlag = ref(true)
const isOpenEyes = ref(false)

const props = defineProps({
  dataParam: {
    type: Array,
    required: true
  }
})

let data1 = reactive(props.dataParam)

watch(props, (newX) => {
  data1 = reactive(newX.dataParam)
})

function onAnswerClick(item) {
    item.selected = !item.selected
}


function onRestart() {
  isOpenEyes.value = false
  data1 = reactive(shuffleSelf(data1))
  data1.forEach(element => {
    element.selected = false
  });
}

function openEyes() {
  isOpenEyes.value = !isOpenEyes.value
  data1.forEach(element => {
    element.selected = true && isOpenEyes.value
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

const handleScroll = () => {
  if (document.documentElement.scrollTop > 200) {
    btnFlag.value = false
  } else {
    btnFlag.value = true
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
    <el-affix :offset="10" style="text-align: right;">
        <Transition name="el-zoom-in-top">
            <el-button v-show="btnFlag" circle :icon="Edit" @click="$emit('showDialog', true)" style="margin: 0 5px 0 0;"></el-button>
        </Transition>
        <Transition name="el-zoom-in-top">
            <el-button v-show="btnFlag" circle :icon="Refresh" @click="onRestart()" style="margin: 0 5px 0 0;"></el-button>
        </Transition>
            <el-button circle :icon="isOpenEyes? View:Hide" @click="openEyes()" style="margin: 0 5px 0 0;"></el-button>
    </el-affix>

    <el-card class="box-card" v-for="item,index in data1">
        <template #header>
            <div class="card-header">
                <span>{{ (index+1) + '. ' + item.question }}</span>
            </div>
        </template>
        <div @click="onAnswerClick(item)">
            <div v-show="item.selected">
                {{ item.answer }}
            </div>
            <div v-show="!item.selected" style="display: flex;justify-content: center;">
                <img src="../assets/closeeyes.png" alt="捂眼" height="50">
            </div>
        </div>
    </el-card>
</template>

<style scoped>
.box-card {
  margin-top: 10px;
}
</style>