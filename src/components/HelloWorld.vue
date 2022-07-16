<template>
<div style="height: 400px">
  <el-auto-resizer>
    <template #default="{ height, width }">
      <el-table-v2
        :columns="columns"
        :data="data"
        :width="width"
        :height="height"
        fixed
      />
    </template>
  </el-auto-resizer>
</div>

<button @click="setupColumns">宽度+</button>
</template>

<script setup>
import { ref } from 'vue'
import ResizableHeader from './ResizableHeader.vue'

const generateData = (
  length = 200
) =>
  Array.from({ length }).map((_, rowIndex) => {
    return {
      column1: `Row ${rowIndex} - Col 1`,
      column2: `Row ${rowIndex} - Col 2`,
      column3: `Row ${rowIndex} - Col 3`
    }
  })

const column1width = ref(150)
const column2width = ref(150)

const columns = ref([])
const data = generateData(1000)

function setupColumns () {
  columns.value = [{
    key: 'column1',
    dataKey: 'column1',
    title: 'column1',
    width: column1width.value,
    headerCellRenderer: (data) => {
      return <ResizableHeader
        uniqueKey={'column1'}
        title={data.column.title}
        columnWidth={column1width.value}
        onColumnWidthChanged={(width) => {
          column1width.value = width
          setupColumns()
        }}
      />
    }
  },
  {
    key: 'column2',
    dataKey: 'column2',
    title: 'column2',
    width: column2width.value,
    headerCellRenderer: (data) => {
      return <ResizableHeader
        uniqueKey={'column2'}
        title={data.column.title}
        columnWidth={column2width.value}
        onColumnWidthChanged={(width) => {
          column2width.value = width
          setupColumns()
        }}
      />
    }
  },
  {
    key: 'column3',
    dataKey: 'column3',
    title: 'column3',
    width: 150
  }]
}
setupColumns()
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
:deep(.el-table-v2__header-cell) {
  overflow: visible;
}
</style>
