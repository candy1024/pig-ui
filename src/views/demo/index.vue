<template>
<div class="layout-box">
  <el-card style="max-width: 300px" >
    <template #header>Yummy hamburger</template>
    <img
      src="https://shadow.elemecdn.com/app/element/hamburger.9cf7b091-55e9-11e9-a976-7f4d0b07eef6.png"
      style="width: 100%"
    />
    <template #footer>
      <el-button>Default</el-button>
      <el-button type="success">Success</el-button>
    </template>
  </el-card>
  
  <el-button id="edit" type="primary" @click="switchEdit">{{isEdit ? 'editing' : 'edit'}}</el-button>
  <span class="target" ref="targetRef" >Vue Moveable1</span>
  <span class="target" ref="targetRef2" >Vue Moveable2</span>


    <Moveable
      v-if="isEdit && currentTarget"
      className="moveable"
      :target="currentTarget"
      :renderDirections="['nw','ne','sw','se']"
      :draggable="true"
      :resizable="true"
      @drag="onDrag"
      @resize="onResize"
    />
</div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import Moveable from "vue3-moveable";

const isEdit = ref(false);
const targetRef = ref();
const targetRef2 = ref();
const currentTarget = ref();

const onDrag = (data: any) => {
  const {transform} = data;
  currentTarget.value.style.transform = transform;
}

const onResize = ({ drag, width, height }: any) => {
    currentTarget.value.style.width = `${width}px`;
    currentTarget.value.style.height = `${height}px`;
    currentTarget.value.style.transform = drag.transform;
}

function switchEdit() {
  isEdit.value = !isEdit.value;
}

function cancelStyle() {
  const domList = [...document.getElementsByClassName('target')];
  domList.forEach((item: any )=> {
    item.style.cursor = 'default';
  })
}

function bindRightClick() {
  document.addEventListener("contextmenu", (e) => {
    e.preventDefault();
    if ((e.target as Element).className === 'target' && isEdit.value) {
      cancelStyle();
      (e.target as HTMLElement).style.cursor = 'move';
      currentTarget.value = e.target;
    }
  });
}

watch(isEdit, (newVal, oldVal) => {
  if (!newVal) {
    cancelStyle();
  }
})

onMounted(() => {
  bindRightClick();
});


</script>

<style lang="scss" scoped>
.layout-box {
  position: relative;
  height: 100vh;
  border: 4px solid #000;
  padding: 10px;

  #edit {
    position: absolute;
    top: 10px;
    right: 10px;
  }

  .target {
    // position: absolute;
    display: inline-block;
    border: 1px solid #000;

  }
}


</style>