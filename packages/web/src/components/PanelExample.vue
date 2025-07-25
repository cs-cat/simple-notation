<template>
  <Card class="max-w-[1200px] w-full mt-5 mx-auto overflow-hidden">
    <template v-slot:title> 🎵简谱示例 </template>
    <div class="flex items-end gap-4 mb-3 flex-wrap">
      <div class="min-w-[120px]">
        <label for="filter-name" class="font-medium text-[#333] whitespace-nowrap mb-1 block"
          >乐谱名称：</label
        >
        <input
          id="filter-name"
          v-model="filterName"
          type="text"
          placeholder="请输入乐谱名称..."
          class="p-2 px-3 border border-[#ddd] rounded text-sm bg-white bg-opacity-80 w-full"
          @input="onFilterInput"
        />
      </div>
      <div class="min-w-[120px]">
        <label for="filter-type" class="font-medium text-[#333] whitespace-nowrap mb-1 block"
          >模板类型：</label
        >
        <select
          id="filter-type"
          class="min-w-[80px] p-2 px-3 border border-[#ddd] rounded text-sm bg-white bg-opacity-80 w-full"
          v-model="filterType"
          @change="onFilterInput"
        >
          <option value="">全部</option>
          <option :value="SNDataType.TEMPLATE">模板</option>
          <option :value="SNDataType.ABC">abc</option>
        </select>
      </div>
      <div class="min-w-[120px]">
        <label for="filter-status" class="font-medium text-[#333] whitespace-nowrap mb-1 block"
          >完成状态：</label
        >
        <select
          id="filter-status"
          class="min-w-[80px] p-2 px-3 border border-[#ddd] rounded text-sm bg-white bg-opacity-80 w-full"
          v-model="filterStatus"
          @change="onFilterInput"
        >
          <option value="">全部</option>
          <option value="finished">已完成</option>
          <option value="unfinished">未完成</option>
        </select>
      </div>
      <button
        class="py-2 px-3 text-sm border border-[#ddd] bg-white text-[#333] rounded cursor-pointer transition duration-200 hover:bg-[#f0f0f0]"
        @click="resetFilter"
      >
        重置
      </button>
    </div>
    <div class="flex flex-wrap gap-2">
      <Button
        class="relative"
        type="ghost"
        color="#7b5aff"
        v-for="example in filteredExamples"
        :key="example.name"
        @click="handleClick(example)"
      >
        {{ example.name }}
        <!-- abc类型角标 -->
        <span
          v-if="example.type === SNDataType.ABC"
          class="absolute right-0 bottom-0 w-0 h-0 border-b-[15px] border-l-[15px] border-b-[#ff6b3d] border-l-transparent z-10 pointer-events-none"
        >
          <span
            class="absolute right-0 bottom-[-15px] text-white text-[10px] font-bold z-20 pointer-events-none whitespace-nowrap"
            >a</span
          >
        </span>
        <span
          v-if="!example.isFinished"
          class="absolute top-[-7px] right-[-1px] bg-[#ffb84d] text-white text-[10px] rounded-[6px] px-[6px] pointer-events-none z-20"
          >未完成</span
        >
      </Button>
    </div>
    <template v-slot:footer>
      <p>
        🎶 如果你有自制的曲谱，欢迎前往
        <a
          href="https://github.com/Encaik/simple-notation/issues/2"
          target="_blank"
          class="text-[#ff6b3d] underline"
          >曲谱分享区</a
        >
        秀一秀你的作品，说不定下一个"曲库之星"就是你！😉
      </p>
    </template>
  </Card>
</template>

<script setup lang="ts">
import { SNDataType } from 'simple-notation';
import { defineEmits, ref, computed } from 'vue';
import { examples } from './examples';
import type { Example } from '../model';

const emits = defineEmits(['load-example']);

const handleClick = (example: Example) => {
  emits('load-example', example);
};

// 筛选相关状态
const filterName = ref('');
const filterType = ref('');
const filterStatus = ref('');

// 防抖处理
let filterTimer: number | null = null;
const onFilterInput = () => {
  if (filterTimer) clearTimeout(filterTimer);
  filterTimer = window.setTimeout(() => {
    filterTimer = null;
    // 触发筛选，computed会自动更新
  }, 200);
};

const resetFilter = () => {
  filterName.value = '';
  filterType.value = '';
  filterStatus.value = '';
};

// 计算筛选后的示例
const filteredExamples = computed(() => {
  return examples.filter((ex) => {
    const matchName = !filterName.value || ex.name.includes(filterName.value);
    const matchType = !filterType.value || ex.type === filterType.value;
    const matchStatus =
      !filterStatus.value ||
      (filterStatus.value === 'finished' && ex.isFinished) ||
      (filterStatus.value === 'unfinished' && !ex.isFinished);
    return matchName && matchType && matchStatus;
  });
});
</script>
