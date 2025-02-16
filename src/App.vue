<script setup lang="ts">
import { ref, computed } from "vue";

// 控制是否进入表白成功页面
const isSuccess = ref(false);
// 主图片初始路径
const mainImageSrc = ref("images/heart.png");
// 问题文本（固定）
const questionText = ref("可以成为我的恋人吗？");
// “可以” 按钮默认文字
const yesButtonDefaultText = "可以";
// “不要” 按钮默认文字
const noButtonDefaultText = "不要";
// 表白文本（固定）
const yesText = ref("!!!喜欢你!! ( >᎑<)♡︎ᐝ");
// 表白图片路径
const yesImageSrc = ref("images/hug.png");

const clickCount = ref(0);  // 记录点击 No 的次数

// No 按钮的文字变化
const noTexts = [
  "？你认真的吗…",
  "要不再想想？",
  "不许选这个！",
  "我会很伤心…",
  "不行:("
];

// 让 Yes 变大，每次放大 2 倍
const yesButtonStyle = computed(() => ({
  transform: `scale(${1 + clickCount.value * 1.2})`
}));

// 挤压 No 按钮，每次右移 100px
const noButtonStyle = computed(() => ({
  transform: `translateX(${clickCount.value * 50}px)`
}));

// **新增：让图片和文字往上移动**
const mainImageStyle = computed(() => ({
  transform: `translateY(-${clickCount.value * 25}px)`,
  transition: "all 0.3s ease"
}));
const questionStyle = computed(() => ({
  transform: `translateY(-${clickCount.value * 25}px)`
}));

// No 文案变化（前 5 次变化）
const noButtonText = computed(() => {
  if (clickCount.value > 0 && clickCount.value <= 5) {
    return noTexts[clickCount.value - 1];
  }
  return noButtonDefaultText;
});

// No 按钮点击事件
function onNoClick() {
  clickCount.value++;

  // 图片变化（前 5 次变化）
  if (clickCount.value === 1) mainImageSrc.value = "images/shocked.png"; // 震惊
  if (clickCount.value === 2) mainImageSrc.value = "images/think.png";   // 思考
  if (clickCount.value === 3) mainImageSrc.value = "images/angry.png";   // 生气
  if (clickCount.value === 4) mainImageSrc.value = "images/crying.png";  // 哭
  if (clickCount.value >= 5) mainImageSrc.value = "images/crying.png";  // 之后一直是哭
}

// Yes 按钮点击后，进入表白成功页面
function onYesClick() {
  isSuccess.value = true;
}
</script>

<template>
  <div v-if="!isSuccess" class="container">
    <img id="mainImage" :src="mainImageSrc" :style="mainImageStyle" />
    <h1 :style="questionStyle">{{ questionText }}</h1>
    <div>
      <button id="yes" :style="yesButtonStyle" @click="onYesClick">{{ yesButtonDefaultText }}</button>
      <button id="no" :style="noButtonStyle" @click="onNoClick">{{ noButtonText }}</button>
    </div>
  </div>
  <div v-else class="yes-screen">
    <h1 class="yes-text">{{ yesText }}</h1>
    <img :src="yesImageSrc" class="yes-image" />
  </div>
</template>
