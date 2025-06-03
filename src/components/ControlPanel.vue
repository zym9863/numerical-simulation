<template>
  <div class="control-panel">
    <div>
      <label for="structure-type">选择数据结构: </label>
      <select id="structure-type" v-model="selectedStructure" @change="emitStructureChange">
        <option value="array">数组 (Array)</option>
        <option value="linked-list">链表 (Linked List)</option>
        <option value="stack">栈 (Stack)</option>
        <option value="queue">队列 (Queue)</option>
        <!-- 后续可以添加更多数据结构类型 -->
      </select>
    </div>

    <div class="data-input-section">
      <label for="data-input">输入数据 (逗号分隔): </label>
      <input type="text" id="data-input" v-model="dataInput" @input="emitDataChange" />
      <button @click="usePresetData">使用预设数据</button>
    </div>

    <div class="operations-section">
      <label>操作: </label>
      <!-- 操作按钮将根据所选数据结构动态生成 -->
      <button v-if="selectedStructure === 'array'" @click="emitOperation('array_add_element')">添加元素</button>
      <button v-if="selectedStructure === 'array'" @click="emitOperation('array_remove_element')">删除元素</button>
      <button v-if="selectedStructure === 'linked-list'" @click="emitOperation('linkedlist_insert_node')">插入节点</button>
      <button v-if="selectedStructure === 'linked-list'" @click="emitOperation('linkedlist_delete_node')">删除节点</button>
      <button v-if="selectedStructure === 'stack'" @click="emitOperation('stack_push')">入栈 (Push)</button>
      <button v-if="selectedStructure === 'stack'" @click="emitOperation('stack_pop')">出栈 (Pop)</button>
      <button v-if="selectedStructure === 'queue'" @click="emitOperation('queue_enqueue')">入队 (Enqueue)</button>
      <button v-if="selectedStructure === 'queue'" @click="emitOperation('queue_dequeue')">出队 (Dequeue)</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const emit = defineEmits(['structure-change', 'data-change', 'operation-request']);

const selectedStructure = ref('array');
const dataInput = ref('');

const emitStructureChange = () => {
  emit('structure-change', selectedStructure.value);
  // 清空数据输入和操作，因为数据结构类型已更改
  dataInput.value = '';
  emitDataChange(); // 触发一次数据更新，以清空父组件中的数据
};

const emitDataChange = () => {
  const parsedData = dataInput.value.split(',').map(item => item.trim()).filter(item => item !== '');
  emit('data-change', parsedData);
};

const usePresetData = () => {
  let preset = [];
  switch (selectedStructure.value) {
    case 'array':
      preset = ['10', '20', '30', '40', '50'];
      break;
    case 'linked-list':
      preset = ['A', 'B', 'C'];
      break;
    case 'stack':
      preset = ['S1', 'S2', 'S3'];
      break;
    case 'queue':
      preset = ['Q1', 'Q2', 'Q3'];
      break;
  }
  dataInput.value = preset.join(',');
  emitDataChange();
};

const emitOperation = (operationType: string) => {
  // 对于需要额外参数的操作，可以在这里处理或在父组件中处理
  let operationData = null;
  if (operationType.includes('add') || operationType.includes('insert') || operationType.includes('push') || operationType.includes('enqueue')) {
    const value = prompt(`请输入要${getOperationName(operationType)}的值:`);
    if (value === null || value.trim() === '') return; // 用户取消或输入为空
    operationData = value.trim();
  } else if (operationType.includes('remove') || operationType.includes('delete')) {
    // 对于删除操作，可能需要指定索引或值，这里简化处理，默认删除最后一个或特定逻辑
    // 如果需要更复杂的删除逻辑，例如按值删除或按索引删除，需要进一步完善
    if (selectedStructure.value === 'array' || selectedStructure.value === 'linked-list') {
        const value = prompt(`请输入要${getOperationName(operationType)}的值/索引:`);
        if (value === null || value.trim() === '') return;
        operationData = value.trim();
    }
  }
  emit('operation-request', { type: operationType, data: operationData });
};

const getOperationName = (type: string) => {
  if (type.includes('add') || type.includes('insert')) return '添加/插入';
  if (type.includes('push')) return '入栈';
  if (type.includes('enqueue')) return '入队';
  if (type.includes('remove') || type.includes('delete')) return '删除';
  return '执行';
}

</script>

<style scoped>
.control-panel {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(15px);
  border-radius: 20px;
  padding: 30px;
  margin-bottom: 30px;
  box-shadow: 
    0 8px 32px rgba(0, 0, 0, 0.1),
    0 2px 16px rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.3);
  animation: fadeInLeft 0.6s ease-out;
}

.control-panel > div {
  margin-bottom: 25px;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 15px;
}

.control-panel label {
  font-weight: 600;
  color: #4a5568;
  font-size: 1.05em;
  min-width: fit-content;
}

.control-panel select,
.control-panel input[type="text"] {
  flex: 1;
  min-width: 200px;
  padding: 14px 18px;
  border: 2px solid rgba(226, 232, 240, 0.8);
  border-radius: 12px;
  font-size: 1em;
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  transition: all 0.3s ease;
  outline: none;
}

.control-panel select:focus,
.control-panel input[type="text"]:focus {
  border-color: #667eea;
  box-shadow: 
    0 0 0 3px rgba(102, 126, 234, 0.1),
    0 4px 12px rgba(102, 126, 234, 0.15);
  background: rgba(255, 255, 255, 1);
  transform: translateY(-1px);
}

.control-panel button {
  padding: 12px 20px;
  border: none;
  border-radius: 12px;
  font-size: 0.95em;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
  margin: 4px;
  min-width: 120px;
}

.control-panel button::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
  transition: left 0.5s;
}

.control-panel button:hover::before {
  left: 100%;
}

.control-panel button:hover {
  transform: translateY(-2px);
}

.control-panel button:active {
  transform: translateY(0);
}

/* 预设数据按钮样式 */
.data-input-section button {
  background: linear-gradient(135deg, #48bb78 0%, #38a169 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(72, 187, 120, 0.3);
}

.data-input-section button:hover {
  box-shadow: 0 8px 25px rgba(72, 187, 120, 0.4);
}

/* 操作按钮样式 */
.operations-section {
  border-top: 1px solid rgba(226, 232, 240, 0.6);
  padding-top: 20px;
  margin-top: 10px;
}

.operations-section button {
  background: linear-gradient(135deg, #ed8936 0%, #dd6b20 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(237, 137, 54, 0.3);
}

.operations-section button:hover {
  box-shadow: 0 8px 25px rgba(237, 137, 54, 0.4);
}

/* 特殊操作按钮 */
.operations-section button:nth-child(odd) {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.operations-section button:nth-child(odd):hover {
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.4);
}

/* 响应式设计 */
@media (max-width: 768px) {
  .control-panel {
    padding: 20px;
    margin-bottom: 20px;
  }
  
  .control-panel > div {
    flex-direction: column;
    align-items: stretch;
    gap: 10px;
  }
  
  .control-panel select,
  .control-panel input[type="text"] {
    min-width: auto;
    width: 100%;
  }
  
  .control-panel button {
    width: 100%;
    margin: 2px 0;
  }
  
  .operations-section {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
  }
}

/* 添加结构类型图标 */
.control-panel select option {
  padding: 10px;
}

/* 输入提示动画 */
.control-panel input[type="text"]::placeholder {
  color: #a0aec0;
  transition: opacity 0.3s ease;
}

.control-panel input[type="text"]:focus::placeholder {
  opacity: 0.6;
}
</style>
