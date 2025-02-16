<script setup lang="ts">
import { ref, computed } from "vue";

const params = new URLSearchParams(window.location.search);
const username = params.get("name");

// 限制用户名长度，避免页面样式崩坏
const maxLength = 20;
const safeUsername = username ? username.substring(0, maxLength) : "???";

// 表白图片路径
const yesImageSrc = ref("images/hug.png");

// 控制是否进入表白成功页面
const isSuccess = ref(false);

const clickCount = ref(0); // 记录点击 No 的次数

// No 按钮的文字变化
const noTexts = [
  ["不要", "heart"],
  ["？你认真的吗…", "shocked"],
  ["要不再想想？", "think"],
  ["不许选这个！", "angry"],
  ["我会很伤心…", "crying"],
  ["不行:(", "crying"]
];

// 主图片初始路径
const mainImageSrc = computed(() => {
  if (clickCount.value >= noTexts.length) {
    return `images/${noTexts[noTexts.length - 1][1]}.png`;
  }

  return `images/${noTexts[clickCount.value][1]}.png`;
});

// 让 Yes 变大，每次放大 2 倍
const yesButtonStyle = computed(() => {
  if (clickCount.value <= 0) {
    return;
  }

  return {
    transform: `scale(${1 + clickCount.value * 1.2})`,
    margin: `${22 * (1 + clickCount.value * 1.2)}px ${36 * (1 + clickCount.value * 1.2)}px`
  }
});

const noButtonStyle = computed(() => {
  if (clickCount.value <= 0) {
    return;
  }

  return {
    transform: `scale(${1 - clickCount.value * 0.1})`
  }
});

// No 文案变化（前 5 次变化）
const noButtonText = computed(() => {
  if (clickCount.value >= noTexts.length) {
    return noTexts[noTexts.length - 1][0];
  }

  return noTexts[clickCount.value][0];
});

const questionText = computed(() => {
  // 防止 `null` 变成 `"null"`
  if (!username) {
    return "可以成为我的恋人吗？";
  }

  return `可以成为我的恋人吗？${safeUsername}`;
});

const yesText = computed(() => {
  // 防止 `null` 变成 `"null"`
  if (!username) {
    return "!!!喜欢你!! ( >᎑<)♡︎ᐝ";
  }

  return `!!!喜欢你!! ( >᎑<)♡︎ᐝ  ${safeUsername}  ♡︎ᐝ(>᎑< )`;
});

// No 按钮点击事件
function onNoClick() {
  clickCount.value++;
}

// Yes 按钮点击后，进入表白成功页面
function onYesClick() {
  isSuccess.value = true;
}
</script>

<template>
  <div v-if="!isSuccess" class="container">
    <img id="mainImage" :src="mainImageSrc" />
    <h1>{{ questionText }}</h1>
    <div class="buttons">
      <button id="yes" :style="yesButtonStyle" @click="onYesClick">可以</button>
      <button id="no" :style="noButtonStyle" @click="onNoClick">{{ noButtonText }}</button>
    </div>
  </div>
  <div v-else class="yes-screen">
    <h1 class="yes-text">{{ yesText }}</h1>
    <img :src="yesImageSrc" class="yes-image" />
  </div>
</template>
