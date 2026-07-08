# 原子化CSS工具类使用指南

## 概述

本项目引入了类似 UnoCSS 的原子化 CSS 工具类系统，提供了丰富的预定义样式类，可以快速构建界面而无需编写自定义样式。

## 已完成的工作

✅ 创建 `styles/utils.wxss` 工具类文件  
✅ 在 `app.wxss` 中全局引入工具类  
✅ 支持所有页面和组件直接使用

## 工具类分类

### 1. Flex 布局

```xml
<!-- 基础flex容器 -->
<view class="flex items-center justify-between">
  <text>左侧</text>
  <text>右侧</text>
</view>

<!-- 垂直居中 -->
<view class="flex-center">
  <text>完全居中</text>
</view>

<!-- 纵向布局 -->
<view class="flex-col gap-4">
  <text>项目1</text>
  <text>项目2</text>
</view>
```

**常用组合类：**
- `flex-center` - 水平垂直居中
- `flex-ic` - 垂直居中
- `flex-between` - 两端对齐且垂直居中
- `flex-col-ic` - 纵向布局且水平居中

### 2. 间距系统

```xml
<!-- Padding -->
<view class="p-4">内边距16rpx</view>
<view class="px-6 py-4">水平24rpx 垂直16rpx</view>
<view class="pt-8">上边距32rpx</view>

<!-- Margin -->
<view class="m-4">外边距16rpx</view>
<view class="mx-auto">水平居中</view>
<view class="mt-6 mb-4">上24rpx 下16rpx</view>

<!-- Gap（用于flex容器） -->
<view class="flex gap-4">
  <text>项目1</text>
  <text>项目2</text>
</view>
```

**间距比例：** 1单位 = 4rpx
- `1` = 4rpx
- `2` = 8rpx
- `3` = 12rpx
- `4` = 16rpx
- `6` = 24rpx
- `8` = 32rpx
- `12` = 48rpx

### 3. 字体排版

```xml
<!-- 字体大小 -->
<text class="text-xs">极小 20rpx</text>
<text class="text-sm">小 24rpx</text>
<text class="text-base">基础 28rpx</text>
<text class="text-lg">大 32rpx</text>
<text class="text-xl">特大 36rpx</text>

<!-- 字体粗细 -->
<text class="font-normal">正常</text>
<text class="font-medium">中等</text>
<text class="font-semibold">半粗</text>
<text class="font-bold">粗体</text>

<!-- 文本对齐 -->
<text class="text-center">居中</text>
<text class="text-right">右对齐</text>

<!-- 文本截断 -->
<text class="truncate">单行截断显示...</text>
<text class="line-clamp-2">最多显示两行，超出部分...</text>
```

### 4. 颜色

```xml
<!-- 文本颜色 -->
<text class="text-gray-900">深灰色标题</text>
<text class="text-gray-500">中灰色副文本</text>
<text class="text-primary">主题色</text>
<text class="text-danger">危险色/错误</text>

<!-- 背景颜色 -->
<view class="bg-white">白色背景</view>
<view class="bg-gray-50">浅灰背景</view>
<view class="bg-primary">主题色背景</view>
```

### 5. 尺寸

```xml
<!-- 宽度 -->
<view class="w-full">100%宽度</view>
<view class="w-1-2">50%宽度</view>
<view class="w-1-3">33.33%宽度</view>

<!-- 高度 -->
<view class="h-full">100%高度</view>
<view class="min-h-screen">最小100vh</view>
```

### 6. 边框圆角

```xml
<!-- 圆角 -->
<view class="rounded">8rpx圆角</view>
<view class="rounded-lg">16rpx圆角</view>
<view class="rounded-full">完全圆形</view>

<!-- 边框 -->
<view class="border border-gray-200">1rpx边框</view>
<view class="border-b border-gray-100">仅底部边框</view>
```

### 7. 阴影

```xml
<view class="shadow-sm">细微阴影</view>
<view class="shadow">标准阴影</view>
<view class="shadow-lg">较大阴影</view>
```

### 8. 其他常用

```xml
<!-- 定位 -->
<view class="relative">相对定位</view>
<view class="absolute top-0 right-0">绝对定位到右上角</view>

<!-- 显示/隐藏 -->
<view class="hidden">隐藏</view>
<view class="block">块级显示</view>

<!-- 透明度 -->
<view class="opacity-50">50%透明度</view>

<!-- 溢出 -->
<view class="overflow-hidden">隐藏溢出</view>
<scroll-view class="overflow-y-auto">纵向滚动</scroll-view>
```

## 实战示例

### 卡片组件

```xml
<view class="bg-white rounded-lg p-4 shadow-md">
  <view class="flex-between mb-3">
    <text class="text-lg font-semibold text-gray-900">标题</text>
    <text class="text-sm text-gray-500">副标题</text>
  </view>
  <text class="text-base text-gray-700 line-clamp-2">
    这是卡片内容，最多显示两行...
  </text>
  <view class="flex gap-2 mt-4">
    <view class="flex-1 bg-primary text-white text-center py-2 rounded">
      主按钮
    </view>
    <view class="flex-1 border border-primary text-primary text-center py-2 rounded">
      次按钮
    </view>
  </view>
</view>
```

### 列表项

```xml
<view class="flex-ic px-4 py-3 border-b border-gray-100">
  <image class="w-48 h-48 rounded-full mr-3" src="{{avatar}}" />
  <view class="flex-1">
    <text class="text-base font-medium text-gray-900">用户名</text>
    <text class="text-sm text-gray-500 mt-1">个人简介</text>
  </view>
  <text class="text-primary">关注</text>
</view>
```

### 表单输入

```xml
<view class="p-4">
  <text class="text-sm text-gray-700 mb-2">用户名</text>
  <input 
    class="w-full px-3 py-2 border border-gray-200 rounded bg-gray-50"
    placeholder="请输入用户名"
  />
</view>
```

## 优势

1. **开发效率高** - 无需编写自定义样式，直接组合使用工具类
2. **样式统一** - 统一的间距、颜色、字体规范，保证视觉一致性
3. **易于维护** - 修改工具类定义即可全局生效
4. **代码简洁** - HTML和样式在同一处，提高可读性
5. **学习成本低** - 类名语义化，见名知义

## 注意事项

- 工具类已全局引入，所有页面和组件可直接使用
- 间距单位使用 rpx，自动适配不同屏幕
- 如需特殊样式，仍可在页面级 `.wxss` 中自定义
- 建议优先使用工具类，减少自定义样式

## 扩展

如需添加新的工具类，请编辑 `styles/utils.wxss` 文件。
