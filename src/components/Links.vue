<template>
  <div v-if="siteLinks[0]" class="links">
    <div class="line">
      <Icon size="20">
        <Link />
      </Icon>
      <span class="title">书签</span>
    </div>
    <!-- 网站列表 -->
    <Swiper
      v-if="siteLinks[0]"
      :modules="[Pagination, Mousewheel]"
      :slides-per-view="1"
      :space-between="40"
      :pagination="{
        el: '.swiper-pagination',
        clickable: true,
        bulletElement: 'div',
      }"
      :mousewheel="true"
    >
      <SwiperSlide v-for="site in siteLinksList" :key="site">
        <el-row class="link-all" :gutter="20">
          <el-col v-for="(item, index) in site" :span="8" :key="item">
            <div
              class="item cards"
              :style="index < 3 ? 'margin-bottom: 20px' : null"
              @click="jumpLink(item)"
            >
              <Icon size="26">
                <component :is="siteIcon[item.icon]" />
              </Icon>
              <span class="name text-hidden">{{ item.name }}</span>
            </div>
          </el-col>
        </el-row>
      </SwiperSlide>
      <div class="swiper-pagination" />
    </Swiper>
    <el-dialog v-model="favoritesOpen" title="我的收藏" width="520px" class="favorites-dialog">
      <div class="favorite-form">
        <el-input v-model="favoriteForm.name" placeholder="名称" clearable />
        <el-input v-model="favoriteForm.link" placeholder="链接，例如 https://ice666.ccwu.cc" clearable />
        <el-button type="primary" @click="addFavorite">添加</el-button>
      </div>
      <div class="favorite-list">
        <div v-for="(item, index) in favoriteLinks" :key="`${item.name}-${item.link}`" class="favorite-item">
          <button type="button" @click="openFavorite(item)">
            <span class="favorite-name text-hidden">{{ item.name }}</span>
            <span class="favorite-link text-hidden">{{ item.link }}</span>
          </button>
          <button type="button" class="remove" @click="removeFavorite(index)">删除</button>
        </div>
        <div v-if="!favoriteLinks.length" class="empty">还没有收藏</div>
      </div>
    </el-dialog>
  </div>
</template>

<script setup>
import { Icon } from "@vicons/utils";
// 可前往 https://www.xicons.org 自行挑选并在此处引入
import { Link, Blog, Bookmark, Cloud, Compass, Globe, Home } from "@vicons/fa"; // 注意使用正确的类别
import { Swiper, SwiperSlide } from "swiper/vue";
import { Pagination, Mousewheel } from "swiper/modules";
import siteLinks from "@/assets/siteLinks.json";

const FAVORITE_STORAGE_KEY = "custom_favorites";
const favoritesOpen = ref(false);
const favoriteForm = reactive({
  name: "",
  link: "",
});
const favoriteLinks = ref([]);

// 计算网站链接
const siteLinksList = computed(() => {
  const result = [];
  for (let i = 0; i < siteLinks.length; i += 6) {
    const subArr = siteLinks.slice(i, i + 6);
    result.push(subArr);
  }
  return result;
});

// 网站链接图标
const siteIcon = {
  Blog,
  Bookmark,
  Cloud,
  Compass,
  Globe,
  Home,
};

// 链接跳转
const jumpLink = (data) => {
  if (data.action === "favorites") {
    favoritesOpen.value = true;
    return;
  }
  window.open(data.link, "_blank");
};

const normalizeLink = (link) => {
  const value = link.trim();
  if (!value) return "";
  return /^https?:\/\//.test(value) ? value : `https://${value}`;
};

const saveFavorites = () => {
  localStorage.setItem(FAVORITE_STORAGE_KEY, JSON.stringify(favoriteLinks.value));
};

const addFavorite = () => {
  const name = favoriteForm.name.trim();
  const link = normalizeLink(favoriteForm.link);
  if (!name || !link) {
    ElMessage({
      message: "请填写名称和链接",
      grouping: true,
    });
    return;
  }
  favoriteLinks.value.unshift({ name, link });
  favoriteForm.name = "";
  favoriteForm.link = "";
  saveFavorites();
};

const removeFavorite = (index) => {
  favoriteLinks.value.splice(index, 1);
  saveFavorites();
};

const openFavorite = (item) => {
  window.open(item.link, "_blank");
};

onMounted(() => {
  try {
    favoriteLinks.value = JSON.parse(localStorage.getItem(FAVORITE_STORAGE_KEY)) || [];
  } catch {
    favoriteLinks.value = [];
  }
});
</script>

<style lang="scss" scoped>
.links {
  .line {
    margin: 2rem 0.25rem 1rem;
    font-size: 1.1rem;
    display: flex;
    align-items: center;
    animation: fade 0.5s;
    .title {
      margin-left: 8px;
      font-size: 1.15rem;
      text-shadow: 0 0 5px #00000050;
    }
  }
  .swiper {
    left: -10px;
    width: calc(100% + 20px);
    padding: 5px 10px 0;
    z-index: 0;
    .swiper-slide {
      height: 100%;
    }
    .swiper-pagination {
      margin-top: 12px;
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
      :deep(.swiper-pagination-bullet) {
        background-color: #ffd1c7;
        width: 20px;
        height: 4px;
        margin: 0 4px;
        border-radius: 4px;
        opacity: 0.2;
        transition: opacity 0.3s;
        &.swiper-pagination-bullet-active {
          opacity: 1;
        }
        &:hover {
          opacity: 1;
        }
      }
    }
  }
  .link-all {
    height: 220px;
    .item {
      height: 100px;
      width: 100%;
      display: flex;
      align-items: center;
      flex-direction: row;
      justify-content: center;
      padding: 0 10px;
      animation: fade 0.5s;

      &:hover {
        transform: scale(1.02);
        background: rgb(184 124 132 / 32%);
        transition: 0.3s;
      }

      &:active {
        transform: scale(1);
      }

      .name {
        font-size: 1.1rem;
        margin-left: 8px;
      }
      @media (min-width: 720px) and (max-width: 820px) {
        .name {
          display: none;
        }
      }
      @media (max-width: 720px) {
        height: 80px;
      }
      @media (max-width: 460px) {
        flex-direction: column;
        .name {
          font-size: 1rem;
          margin-left: 0;
          margin-top: 8px;
        }
      }
    }
    @media (max-width: 720px) {
      height: 180px;
    }
  }
  .favorite-form {
    display: grid;
    grid-template-columns: 1fr 1.35fr auto;
    gap: 10px;
    margin-bottom: 16px;
    :deep(.el-input__wrapper) {
      background: rgb(255 255 255 / 14%);
      border-radius: 6px;
      box-shadow: none;
    }
    :deep(.el-input__inner) {
      color: #fff;
    }
    :deep(.el-input__inner::placeholder) {
      color: rgb(255 255 255 / 60%);
    }
    :deep(.el-button) {
      border: 0;
      background: rgb(184 124 132 / 74%);
      border-radius: 6px;
    }
  }
  .favorite-list {
    max-height: 300px;
    overflow: auto;
    .favorite-item {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 8px 0;
      button {
        border: 0;
        color: #fff;
        cursor: pointer;
      }
      button:first-child {
        min-width: 0;
        flex: 1;
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        padding: 10px 12px;
        border-radius: 6px;
        background: rgb(255 255 255 / 10%);
        text-align: left;
      }
      .favorite-name {
        width: 100%;
        font-size: 15px;
      }
      .favorite-link {
        width: 100%;
        margin-top: 4px;
        color: rgb(255 255 255 / 62%);
        font-size: 12px;
      }
      .remove {
        padding: 9px 12px;
        border-radius: 6px;
        background: rgb(255 255 255 / 12%);
      }
    }
    .empty {
      padding: 28px 0;
      text-align: center;
      color: rgb(255 255 255 / 62%);
    }
  }
  @media (max-width: 560px) {
    .favorite-form {
      grid-template-columns: 1fr;
    }
  }
}

:global(.favorites-dialog) {
  border-radius: 8px;
  background: rgb(39 49 72 / 82%);
  border: 1px solid rgb(255 209 199 / 18%);
  backdrop-filter: blur(18px);
}

:global(.favorites-dialog .el-dialog__title),
:global(.favorites-dialog .el-dialog__headerbtn .el-dialog__close) {
  color: #fff;
}
</style>
