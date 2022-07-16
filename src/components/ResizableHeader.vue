<template>
<div class="resizable-cell">
  <span>{{ title }}</span>
  <span :id="uniqueKey" class="resize-mark"></span>
</div>
</template>

<script setup>
import { onMounted, onUnmounted, defineProps, defineEmits, nextTick } from 'vue'

const props = defineProps({
  uniqueKey: {
    type: String,
    required: true
  },
  title: {
    type: String,
    default: ''
  },
  columnWidth: {
    type: Number,
    default: 0
  },
  columnMinWidth: {
    type: Number,
    default: 0
  },
  columnMaxWidth: {
    type: Number,
    default: 1000
  }
})
const emits = defineEmits(['columnWidthChanged'])

let columnResizeMark
onMounted(() => {
  nextTick(() => {
    columnResizeMark = document.getElementById(props.uniqueKey)
    if (columnResizeMark) {
      columnResizeMark.addEventListener('mousedown', onColumnMouseDown)
    }
  })
})

let mouseDownX = 0
let mouseMoveX = 0
function onColumnMouseDown (e) {
  // 鼠标按下时x位置
  mouseDownX = e.clientX
  window.addEventListener('mouseup', onColumnMouseUp)
  window.addEventListener('mousemove', onColumnMouseMove)
}
function onColumnMouseUp (e) {
  // 鼠标抬起改变列宽，取消window事件监听
  if (mouseDownX !== 0) {
    const newWidth = props.columnWidth - mouseMoveX
    emits('columnWidthChanged', newWidth > props.columnMinWidth && newWidth < props.columnMaxWidth ? newWidth : props.columnWidth)
    mouseDownX = 0
    columnResizeMark.style.right = '0'
  }
  nextTick(() => {
    window.removeEventListener('mouseup', onColumnMouseUp)
    window.removeEventListener('mousemove', onColumnMouseMove)
  })
}
function onColumnMouseMove (e) {
  // 鼠标向左移动的距离
  mouseMoveX = mouseDownX - e.clientX
  columnResizeMark.style.right = `${mouseMoveX}px`
}

onUnmounted(() => {
  columnResizeMark.removeEventListener('mousedown', onColumnMouseDown)
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.resizable-cell {
  position: relative;
  display: flex;
  width: 100%;
  height: 100%;
  align-items: center;
}
.resizable-cell .resize-mark {
  display: inline-block;
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 5px;
}
.resizable-cell .resize-mark:hover {
  cursor: col-resize;
  z-index: 1000;
}
.resizable-cell .resize-mark:hover::before {
  content: "";
  position: absolute;
  width: 0;
  height: 100%;
  border-left: 1px dashed gray;
}
</style>
