<template>
  <div class="visualizer-container">
    <div v-if="props.selectedStructure === 'array'" class="array-container">
      <div
        v-for="(item, index) in props.structureData"
        :key="index"
        class="array-element"
        :class="{ 'highlight-new': isNewElement(item), 'highlight-removed': isRemovedElement(item) }"
      >
        {{ item }}
        <span class="index-label">{{ index }}</span>
      </div>
      <div v-if="!props.structureData || props.structureData.length === 0" class="empty-structure">
        <div class="empty-icon">📊</div>
        <p>数组为空</p>
      </div>
    </div>

    <div v-else-if="props.selectedStructure === 'linked-list'" class="linked-list-container">
      <!-- 链表可视化 (简化) -->
      <template v-if="props.structureData && props.structureData.length > 0">
        <div
          v-for="(item, index) in props.structureData"
          :key="index"
          class="list-node"
          :class="{ 'highlight-new': isNewElement(item), 'highlight-removed': isRemovedElement(item) }"
        >
          <div class="node-content">
            <span class="node-data">{{ item }}</span>
            <span class="node-pointer">→</span>
          </div>
          <div v-if="index < props.structureData.length - 1" class="arrow">➡️</div>
        </div>
        <div class="null-pointer">NULL</div>
      </template>
      <div v-else class="empty-structure">
        <div class="empty-icon">🔗</div>
        <p>链表为空</p>
      </div>
    </div>

    <div v-else-if="props.selectedStructure === 'stack'" class="stack-container">
      <!-- 栈可视化 -->
      <div v-if="!props.structureData || props.structureData.length === 0" class="empty-structure">
        <div class="empty-icon">📚</div>
        <p>栈为空</p>
      </div>
      <div class="stack-top-indicator" v-if="props.structureData && props.structureData.length > 0">
        <span>栈顶 TOP</span>
        <div class="indicator-arrow">↓</div>
      </div>
      <div
        v-for="(item, index) in [...props.structureData].reverse()"
        :key="index"
        class="stack-element"
        :class="{ 'highlight-new': isNewElement(item), 'highlight-removed': isRemovedElement(item) }"
      >
        {{ item }}
      </div>
      <div class="stack-base" v-if="props.structureData && props.structureData.length > 0">
        栈底 BASE
      </div>
    </div>

    <div v-else-if="props.selectedStructure === 'queue'" class="queue-container">
      <!-- 队列可视化 -->
      <div v-if="props.structureData && props.structureData.length > 0" class="queue-wrapper">
        <div class="queue-indicators">
          <div class="queue-front-indicator">
            <span>队头 FRONT</span>
            <div class="indicator-arrow">↓</div>
          </div>
          <div class="queue-rear-indicator">
            <span>队尾 REAR</span>
            <div class="indicator-arrow">↓</div>
          </div>
        </div>
        <div class="queue-elements">
          <div
            v-for="(item, index) in props.structureData"
            :key="index"
            class="queue-element"
            :class="{ 'highlight-new': isNewElement(item), 'highlight-removed': isRemovedElement(item) }"
          >
            {{ item }}
          </div>
        </div>
        <div class="queue-labels">
          <span class="queue-label">出队方向 →</span>
          <span class="queue-label">← 入队方向</span>
        </div>
      </div>
      <div v-else class="empty-structure">
        <div class="empty-icon">🚶‍♂️</div>
        <p>队列为空</p>
      </div>
    </div>

    <div v-else class="empty-structure">
      <div class="empty-icon">🎯</div>
      <p>请选择一种数据结构并添加数据</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, nextTick } from 'vue';

interface Props {
  selectedStructure: string;
  structureData: any[]; // 根据实际数据结构定义更具体的类型
  operationSteps?: any[]; // 操作步骤，用于动画演示
}

const props = defineProps<Props>();

const newElements = ref<Set<any>>(new Set());
const removedElements = ref<Set<any>>(new Set());

const isNewElement = (item: any) => newElements.value.has(item);
const isRemovedElement = (item: any) => removedElements.value.has(item);

const resetHighlights = () => {
  newElements.value.clear();
  removedElements.value.clear();
};

// 监听数据变化并更新视图的逻辑
watch(() => props.selectedStructure, (newVal) => {
  console.log('Selected structure changed to:', newVal);
  resetHighlights();
  // 根据新的数据结构类型更新可视化
});

watch(() => props.structureData, (newData, oldData) => {
  console.log('Structure data changed:', newData);
  resetHighlights();
  // 简单的差异比较来高亮新元素 (实际动画会更复杂)
  if (newData && oldData) {
    const oldSet = new Set(oldData);
    newData.forEach(item => {
      if (!oldSet.has(item)) {
        newElements.value.add(item);
      }
    });
  } else if (newData) {
    newData.forEach(item => newElements.value.add(item));
  }

  // 根据新的数据更新可视化
}, { deep: true });

watch(() => props.operationSteps, async (steps) => {
  if (steps && steps.length > 0) {
    console.log('Performing operation steps:', steps);
    resetHighlights(); // 开始新操作前清除高亮

    for (const step of steps) {
      // 模拟动画延迟
      await new Promise(resolve => setTimeout(resolve, 500));

      if (step.type === 'add' || step.type === 'insert' || step.type === 'push' || step.type === 'enqueue') {
        newElements.value.add(step.element);
      } else if (step.type === 'remove' || step.type === 'delete' || step.type === 'pop' || step.type === 'dequeue') {
        removedElements.value.add(step.element);
        // 实际数据已在 App.vue 中更新，这里仅高亮
      }
      await nextTick(); // 等待 DOM 更新

      // 动画结束后，可以清除瞬时高亮，或者让其保持直到下一次操作
      // setTimeout(() => {
      //   if (step.type.includes('add') || step.type.includes('insert')) newElements.value.delete(step.element);
      //   if (step.type.includes('remove') || step.type.includes('delete')) removedElements.value.delete(step.element);
      // }, 1000); // 高亮持续1秒
    }
    // 操作完成后，可以清除所有高亮，或根据需求保留
    // setTimeout(resetHighlights, 1500);
  }
}, { deep: true });

</script>

<style scoped>
.visualizer-container {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(15px);
  border-radius: 20px;
  padding: 30px;
  margin-top: 20px;
  min-height: 400px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  box-shadow: 
    0 8px 32px rgba(0, 0, 0, 0.1),
    0 2px 16px rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.3);
  overflow-x: auto;
  position: relative;
  animation: fadeInRight 0.8s ease-out;
}

.empty-structure {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #718096;
  font-style: italic;
  text-align: center;
  padding: 40px;
}

.empty-icon {
  font-size: 3rem;
  margin-bottom: 15px;
  opacity: 0.7;
  animation: bounce 2s infinite;
}

.empty-structure p {
  font-size: 1.1em;
  margin: 0;
  color: #4a5568;
}

/* 通用元素样式 */
.array-element, .list-node, .stack-element, .queue-element {
  padding: 16px 20px;
  margin: 8px;
  border: 2px solid #667eea;
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  border-radius: 16px;
  text-align: center;
  min-width: 60px;
  font-weight: 600;
  font-size: 1.1em;
  color: #2d3748;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.2);
  backdrop-filter: blur(10px);
}

.array-element:hover, .list-node:hover, .stack-element:hover, .queue-element:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 8px 20px rgba(102, 126, 234, 0.3);
}

/* 数组样式 */
.array-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 10px;
  padding: 20px;
  width: 100%;
}

.array-element {
  position: relative;
}

.index-label {
  position: absolute;
  bottom: -25px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 0.8em;
  color: #718096;
  background: rgba(255, 255, 255, 0.9);
  padding: 2px 8px;
  border-radius: 8px;
  border: 1px solid rgba(102, 126, 234, 0.3);
}

/* 链表样式 */
.linked-list-container {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  gap: 15px;
  padding: 20px;
  width: 100%;
}

.list-node {
  display: flex;
  align-items: center;
  padding: 12px;
}

.node-content {
  display: flex;
  align-items: center;
  gap: 8px;
}

.node-data {
  font-weight: 600;
}

.node-pointer {
  font-size: 1.2em;
  color: #667eea;
}

.arrow {
  font-size: 1.5em;
  color: #667eea;
  animation: pulse 2s infinite;
}

.null-pointer {
  padding: 12px 16px;
  background: linear-gradient(135deg, #e53e3e 0%, #c53030 100%);
  color: white;
  border-radius: 12px;
  font-weight: 600;
  box-shadow: 0 4px 12px rgba(229, 62, 62, 0.3);
}

/* 栈样式 */
.stack-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  width: 100%;
  min-height: 350px;
  position: relative;
  justify-content: flex-end;
}

.stack-element {
  width: 200px;
  margin: 2px 0;
  position: relative;
}

.stack-top-indicator, .stack-base {
  font-size: 0.9em;
  color: #4a5568;
  font-weight: 600;
  background: linear-gradient(135deg, #ffd89b 0%, #19547b 100%);
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  margin: 10px 0;
  box-shadow: 0 4px 12px rgba(25, 84, 123, 0.3);
  display: flex;
  align-items: center;
  gap: 8px;
}

.indicator-arrow {
  font-size: 1.2em;
  animation: bounce 1.5s infinite;
}

/* 队列样式 */
.queue-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  width: 100%;
  position: relative;
  gap: 15px;
}

.queue-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  width: 100%;
}

.queue-indicators {
  display: flex;
  justify-content: space-between;
  width: 100%;
  max-width: 400px;
}

.queue-elements {
  display: flex;
  align-items: center;
  gap: 10px;
  overflow-x: auto;
  padding: 15px;
  border-radius: 16px;
  background: rgba(102, 126, 234, 0.05);
  border: 2px dashed rgba(102, 126, 234, 0.3);
  min-width: 300px;
  position: relative;
}

.queue-element {
  flex-shrink: 0;
  min-width: 80px;
}

.queue-front-indicator, .queue-rear-indicator {
  font-size: 0.9em;
  color: white;
  font-weight: 600;
  background: linear-gradient(135deg, #38b2ac 0%, #319795 100%);
  padding: 8px 16px;
  border-radius: 20px;
  box-shadow: 0 4px 12px rgba(56, 178, 172, 0.3);
  display: flex;
  align-items: center;
  gap: 8px;
}

.queue-front-indicator {
  background: linear-gradient(135deg, #f56565 0%, #e53e3e 100%);
  box-shadow: 0 4px 12px rgba(245, 101, 101, 0.3);
}

.queue-labels {
  display: flex;
  justify-content: space-between;
  width: 100%;
  max-width: 400px;
  margin-top: 10px;
}

.queue-label {
  font-size: 0.85em;
  color: #4a5568;
  font-weight: 500;
  background: rgba(255, 255, 255, 0.8);
  padding: 4px 12px;
  border-radius: 12px;
  border: 1px solid rgba(102, 126, 234, 0.2);
}

/* 高亮动画效果 */
.highlight-new {
  background: linear-gradient(135deg, #68d391 0%, #48bb78 100%) !important;
  border-color: #38a169 !important;
  color: white !important;
  transform: scale(1.1);
  animation: pulseGreen 0.8s ease-in-out;
}

.highlight-removed {
  background: linear-gradient(135deg, #fc8181 0%, #e53e3e 100%) !important;
  border-color: #c53030 !important;
  color: white !important;
  opacity: 0.8;
  transform: scale(0.95);
  animation: pulseRed 0.8s ease-in-out;
}

/* 动画关键帧 */
@keyframes pulseGreen {
  0%, 100% { transform: scale(1.1); }
  50% { transform: scale(1.15); }
}

@keyframes pulseRed {
  0%, 100% { transform: scale(0.95); }
  50% { transform: scale(0.9); }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .visualizer-container {
    padding: 20px;
    min-height: 300px;
  }
  
  .array-element, .list-node, .stack-element, .queue-element {
    padding: 12px 16px;
    font-size: 1em;
    min-width: 50px;
  }
  
  .stack-element {
    width: 150px;
  }
  
  .queue-elements {
    min-width: 250px;
  }
  
  .linked-list-container {
    flex-direction: column;
    gap: 10px;
  }
  
  .arrow {
    transform: rotate(90deg);
  }
}

/* 3D 效果 */
.array-element, .stack-element, .queue-element {
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.9), rgba(255, 255, 255, 0.7));
  border: 2px solid rgba(102, 126, 234, 0.4);
}

.array-element::before, .stack-element::before, .queue-element::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 16px;
  z-index: -1;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.array-element:hover::before, .stack-element:hover::before, .queue-element:hover::before {
  opacity: 0.1;
}
</style>
