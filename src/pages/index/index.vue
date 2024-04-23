<script setup lang="ts">
import type { BannerItem, CategoryItem, HotItem } from '@/types/home'
import CustomNavbar from './components/CustomNavbar.vue'
import CategortPanel from './components/CategoryPanel.vue'
import HotPanel from './components/HotPanel.vue'
import { getHomeBannerAPI, getHomeCategoryAPI, getHomeHotAPI } from '@/services/home'
import { ref } from 'vue'
import { onLoad } from '@dcloudio/uni-app'
import XtxGuess from '@/components/XtxGuess.vue'
import type { XtxGuessInstance } from '@/components/components'

const bannerList = ref<BannerItem[]>([])
const categoryList = ref<CategoryItem[]>([])
const HotList = ref<HotItem[]>([])
// 下拉刷新状态
const isTriggered = ref(false)
const guessRef = ref<XtxGuessInstance>()

const getHomeBannerData = async () => {
  const data = await getHomeBannerAPI()
  bannerList.value = data.result
}

const getHomeCategoryData = async () => {
  const data = await getHomeCategoryAPI()
  categoryList.value = data.result
}

const getHomeHotData = async () => {
  const data = await getHomeHotAPI()
  HotList.value = data.result
}

// 自定义下拉刷新被触发
const onRefresherrefresh = async () => {
  console.log('ds')
  // // 开启动画
  isTriggered.value = true
  // // 重置猜你喜欢组件数据
  guessRef.value?.resetData() // 加载数据
  await Promise.all([getHomeBannerData(), getHomeCategoryData(), getHomeHotData()]) // 关闭动画
  isTriggered.value = false
}

// 滑动到底部获取更多
const onScrolltolower = async () => {
  // // 开启动画
  isTriggered.value = true
  // // 重置猜你喜欢组件数据
  guessRef.value?.getMore() // 加载数据
  isTriggered.value = false
}

onLoad(() => {
  getHomeBannerData()
  getHomeCategoryData()
  getHomeHotData()
})
</script>

<template>
  <CustomNavbar />
  <scroll-view
    scroll-y
    class="scroll-view"
    refresher-enabled
    @refresherrefresh="onRefresherrefresh"
    @scrolltolower="onScrolltolower"
    :refresher-triggered="isTriggered"
  >
    <XtxSwiper :list="bannerList" />
    <CategortPanel :list="categoryList" />
    <HotPanel :list="HotList" />
    <XtxGuess ref="guessRef" />
  </scroll-view>
</template>

<style lang="scss">
page {
  background: #f7f7f7;
  display: flex;
  height: 100vh;
  flex-direction: column;
}

.scroll-view {
  // height: 200px;
  flex: 1;
}
</style>
