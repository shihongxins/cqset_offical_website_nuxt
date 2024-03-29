<script setup>
const runtimeConfig = useRuntimeConfig();
const props = defineProps({
  bannerImgFileKey: {
    type: String,
    required: true,
  }
});
const DOMBannerContainerRef = ref(null);
const CompCarouselRef = ref(null);

const { data: bannerImgFiles } = useQueryUploadedFiles({
  key: props.bannerImgFileKey,
});

const bannerImgFileURLs = computed(() => {
  let urls = [];
  if (DOMBannerContainerRef.value && bannerImgFiles.value?.data?.list?.length) {
    urls = bannerImgFiles.value.data.list.filter((file) => file.url).map((file) => {
      return `${runtimeConfig.public.resourceOrigin}/${file.url}?h=${DOMBannerContainerRef.value.offsetHeight}`;
    });
  }
  return urls;
});

const onFocus = ref(false);
const handleFocus = (f = false) => {
  onFocus.value = f;
}
const handleCarouselChange = (increase = 1) => {
  if (CompCarouselRef.value?.pages > 1) {
    let nextPage = CompCarouselRef.value.page + increase;
    if (nextPage < 1 || nextPage > CompCarouselRef.value.pages) {
      nextPage = 1;
    }
    CompCarouselRef.value.select(nextPage);
  }
}

onMounted(() => {
  setInterval(() => {
    if (onFocus.value) return;
    handleCarouselChange();
  }, 2000);
});

</script>

<template>
  <div class="banner__container" ref="DOMBannerContainerRef">
    <UCarousel ref="CompCarouselRef" :items="bannerImgFileURLs" v-slot="{ item }" :ui="{ item: 'basis-full' }"
      :indicators="bannerImgFileURLs.length > 1" @mouseenter="handleFocus(true)" @mouseleave="handleFocus(false)"
      @touchstart="handleFocus(true)" @touchend="handleFocus(false)">
      <img :src="item" alt="banner" class="banner_img" />
    </UCarousel>
  </div>
</template>

<style lang="scss" scoped>
.banner {
  &__container {
    box-sizing: border-box;
    padding-bottom: calc(5 / 16 * 100%); // 16:5
    height: 0;
    position: relative;
    background-color: #ddd;

    >* {
      position: absolute;
      inset: 0;
      overflow: hidden;
    }

    .banner_img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
}
</style>
