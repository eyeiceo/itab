<template>
  <div class="setting">
    <el-collapse class="collapse" v-model="activeName" accordion>
      <el-collapse-item title="个性壁纸" name="1">
        <div class="item">
          <span class="text">{{ wallpaperText }}</span>
        </div>
      </el-collapse-item>
      <el-collapse-item title="开源配置" name="2">
        <div class="item">
          <span class="text">{{ logoText }}</span>
        </div>
        <div class="item">
          <span class="text">{{ linksText }}</span>
        </div>
        <div class="item">
          <span class="text">{{ socialText }}</span>
        </div>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script setup>
// 默认选中项
const activeName = ref("1");
const wallpaperText = import.meta.env.VITE_SETTING_WALLPAPER_TEXT || "背景图片通过 VITE_SITE_BACKGROUND 自定义";
const logoText = import.meta.env.VITE_SETTING_LOGO_TEXT || "主页 Logo 通过 VITE_SITE_MAIN_LOGO 自定义";
const linksText = import.meta.env.VITE_SETTING_LINKS_TEXT || "书签按钮在 src/assets/siteLinks.json 中自定义";
const socialText = import.meta.env.VITE_SETTING_SOCIAL_TEXT || "联系方式在 src/assets/socialLinks.json 中自定义";
</script>

<style lang="scss" scoped>
.setting {
  .collapse {
    border-radius: 8px;
    --el-collapse-content-bg-color: #ffffff10;
    border-color: transparent;
    overflow: hidden;

    :deep(.el-collapse-item__header) {
      background-color: #ffffff30;
      color: #fff;
      font-size: 15px;
      padding-left: 18px;
      border-color: transparent;
    }

    :deep(.el-collapse-item__wrap) {
      border-color: transparent;

      .el-collapse-item__content {
        padding: 20px;
        .item {
          display: flex;
          align-items: center;
          justify-content: space-between;
          flex-wrap: wrap;
          font-size: 14px;
          .el-switch__core {
            border-color: transparent;
            background-color: #ffffff30;
          }
          .el-radio-group {
            .el-radio {
              margin: 2px 10px 2px 0;
              border-radius: 5px;

              &:last-child {
                margin-right: 0;
              }
            }
          }
        }
        .el-radio-group {
          justify-content: space-between;

          .el-radio {
            margin: 10px 16px;
            background: #ffffff26;
            border: 2px solid transparent;
            border-radius: 8px;

            .el-radio__label {
              color: #fff;
            }

            .el-radio__inner {
              background: #ffffff06 !important;
              border: 2px solid #eeeeee !important;
            }

            &.is-checked {
              background: #ffffff06 !important;
              border: 2px solid #eeeeee !important;
            }

            .is-checked {
              .el-radio__inner {
                background-color: #ffffff30 !important;
                border-color: #fff !important;
              }

              & + .el-radio__label {
                color: #fff !important;
              }
            }
          }
        }
      }
    }
  }
}
</style>
