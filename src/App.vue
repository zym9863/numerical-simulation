<template>
  <div id="app-container">
    <h1>数据结构动态演示</h1>
    <ControlPanel
      @structure-change="handleStructureChange"
      @data-change="handleDataChange"
      @operation-request="handleOperationRequest"
    />
    <DataStructureVisualizer
      :selected-structure="currentStructureType"
      :structure-data="currentStructureData"
      :operation-steps="currentOperationSteps"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import DataStructureVisualizer from './components/DataStructureVisualizer.vue';
import ControlPanel from './components/ControlPanel.vue';

const currentStructureType = ref('array'); // 默认数据结构类型
const currentStructureData = ref<any[]>(['10', '20', '30', '40', '50']); // 初始示例数据
const currentOperationSteps = ref<any[]>([]); // 用于动画的操作

const handleStructureChange = (newStructure: string) => {
  currentStructureType.value = newStructure;
  currentStructureData.value = []; // 切换结构时清空数据
  currentOperationSteps.value = []; // 清空操作步骤
  // 根据新结构类型设置默认数据
  switch (newStructure) {
    case 'array':
      currentStructureData.value = ['10', '20', '30'];
      break;
    case 'linked-list':
      currentStructureData.value = ['A', 'B', 'C'];
      break;
    case 'stack':
      currentStructureData.value = ['S1', 'S2'];
      break;
    case 'queue':
      currentStructureData.value = ['Q1', 'Q2', 'Q3'];
      break;
    default:
      currentStructureData.value = [];
  }
};

const handleDataChange = (newData: string[]) => {
  currentStructureData.value = newData;
  currentOperationSteps.value = []; // 数据变化时清空旧操作
};

const handleOperationRequest = (operation: { type: string; data?: any }) => {
  console.log('Operation requested:', operation);
  // 这里将是核心的逻辑处理部分
  // 1. 根据 operation.type 和 operation.data 更新 currentStructureData
  // 2. 生成 currentOperationSteps 来描述变化过程，用于动画

  const steps: any[] = []; // 存储描述操作的步骤

  switch (operation.type) {
    // --- 数组操作 ---
    case 'array_add_element':
      if (operation.data !== undefined && operation.data !== null) {
        const newData = [...currentStructureData.value, operation.data];
        steps.push({ type: 'add', element: operation.data, newArray: [...newData] });
        currentStructureData.value = newData;
      }
      break;
    case 'array_remove_element':
      if (currentStructureData.value.length > 0) {
        const target = operation.data; // 可以是值或索引
        let newData = [...currentStructureData.value];
        let removedElement;
        const indexToRemove = newData.indexOf(target);

        if (indexToRemove > -1) { // 按值删除
          removedElement = newData.splice(indexToRemove, 1)[0];
          steps.push({ type: 'remove', element: removedElement, index: indexToRemove, newArray: [...newData] });
        } else if (!isNaN(parseInt(target)) && parseInt(target) < newData.length && parseInt(target) >= 0) { // 按索引删除
          removedElement = newData.splice(parseInt(target), 1)[0];
           steps.push({ type: 'remove', element: removedElement, index: parseInt(target), newArray: [...newData] });
        } else if (newData.length > 0) { // 默认删除最后一个
            removedElement = newData.pop();
            steps.push({ type: 'remove', element: removedElement, index: newData.length, newArray: [...newData] });
        }
        currentStructureData.value = newData;
      }
      break;

    // --- 链表操作 (简化) ---
    case 'linkedlist_insert_node':
      if (operation.data !== undefined && operation.data !== null) {
        // 简化：默认在末尾插入
        const newData = [...currentStructureData.value, operation.data];
        steps.push({ type: 'insert', element: operation.data, position: 'end', newStructure: [...newData] });
        currentStructureData.value = newData;
      }
      break;
    case 'linkedlist_delete_node':
       if (currentStructureData.value.length > 0) {
        const target = operation.data; // 可以是值
        let newData = [...currentStructureData.value];
        let removedElement;
        const indexToRemove = newData.indexOf(target);

        if (indexToRemove > -1) { // 按值删除
          removedElement = newData.splice(indexToRemove, 1)[0];
          steps.push({ type: 'delete', element: removedElement, newStructure: [...newData] });
        } else if (newData.length > 0) { // 默认删除最后一个
            removedElement = newData.pop();
            steps.push({ type: 'delete', element: removedElement, newStructure: [...newData] });
        }
        currentStructureData.value = newData;
      }
      break;

    // --- 栈操作 ---
    case 'stack_push':
      if (operation.data !== undefined && operation.data !== null) {
        const newData = [...currentStructureData.value, operation.data];
        steps.push({ type: 'push', element: operation.data, newStack: [...newData] });
        currentStructureData.value = newData;
      }
      break;
    case 'stack_pop':
      if (currentStructureData.value.length > 0) {
        const newData = [...currentStructureData.value];
        const poppedElement = newData.pop();
        steps.push({ type: 'pop', element: poppedElement, newStack: [...newData] });
        currentStructureData.value = newData;
      }
      break;

    // --- 队列操作 ---
    case 'queue_enqueue':
      if (operation.data !== undefined && operation.data !== null) {
        const newData = [...currentStructureData.value, operation.data];
        steps.push({ type: 'enqueue', element: operation.data, newQueue: [...newData] });
        currentStructureData.value = newData;
      }
      break;
    case 'queue_dequeue':
      if (currentStructureData.value.length > 0) {
        const newData = [...currentStructureData.value];
        const dequeuedElement = newData.shift(); // 从头部移除
        steps.push({ type: 'dequeue', element: dequeuedElement, newQueue: [...newData] });
        currentStructureData.value = newData;
      }
      break;

    default:
      console.warn('Unknown operation type:', operation.type);
  }

  currentOperationSteps.value = steps;
  console.log('Updated data:', currentStructureData.value);
  console.log('Operation steps:', currentOperationSteps.value);
};

// 初始化时，根据默认类型设置数据
handleStructureChange(currentStructureType.value);

</script>

<style>
#app-container {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2d3748;
  margin: 0 auto;
  max-width: 1000px;
  padding: 20px;
  animation: fadeInUp 0.8s ease-out;
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
  color: #1a202c;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 40px;
  position: relative;
  letter-spacing: -0.02em;
}

h1::after {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 60px;
  height: 4px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 2px;
}

@media (max-width: 768px) {
  h1 {
    font-size: 2rem;
  }
  
  #app-container {
    padding: 15px;
  }
}
</style>
