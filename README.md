# vue-admin-kit

[![npm version](https://img.shields.io/npm/v/vue-admin-kit.svg)](https://www.npmjs.com/package/vue-admin-kit)
[![license](https://img.shields.io/npm/l/vue-admin-kit.svg)](https://github.com/nicekate/vue-admin-kit/blob/main/LICENSE)

企业级 Vue 3 后台管理组件工具套件，基于 Element Plus 和 VXE-Table 构建，提供配置驱动的 CRUD 页面模板。

📖 **[在线文档](https://vue-admin-kit.jiang.in/)**

## ✨ 特性

- 🚀 **配置驱动** - 通过 JSON 配置快速生成搜索表单、数据表格、详情弹窗、编辑表单
- 📦 **开箱即用** - 内置 14 种表单控件、字典管理、文件上传、权限指令
- 🎨 **高度可定制** - 支持自定义组件、字典适配器、权限控制
- 📱 **响应式设计** - 根据屏幕尺寸自动调整表单项大小
- 🔧 **TypeScript** - 完整的类型定义，提供良好的开发体验

## 📦 安装

```bash
npm install vue-admin-kit
# 或
pnpm add vue-admin-kit
# 或
yarn add vue-admin-kit
```

### Peer Dependencies

```bash
pnpm add vue@^3.4.0 element-plus@^2.0.0 vxe-table@^4.0.0 vxe-pc-ui@^4.0.0 xe-utils@^3.0.0 @element-plus/icons-vue@^2.0.0 @vueuse/core@^10.0.0
```

## 🚀 快速开始

### 1. 引入样式

```typescript
// main.ts
import "vue-admin-kit/style.css";
```

### 2. 初始化配置

```typescript
import { setupPageTemplate } from "vue-admin-kit";
import { getDicts } from "@/api/system/dict";

setupPageTemplate({
  dependencies: {
    dictLoader: async (dictType) => {
      const res = await getDicts(dictType);
      return {
        success: true,
        data: res.data.map((item) => ({
          label: item.dictLabel,
          value: item.dictValue,
          elTagType: item.listClass,
        })),
      };
    },
  },
});
```

### 3. 创建 CRUD 页面

```vue
<script setup lang="ts">
import { ref } from "vue";
import {
  useState,
  PageTemplate,
  Table,
  defineSearchConfig,
  defineFormConfig,
  defineColumnsConfig,
} from "vue-admin-kit";
import type { PageTemplateExposed } from "vue-admin-kit";

const pageTemplateRef = ref<PageTemplateExposed>();

const searchConfig = defineSearchConfig([
  { type: "input", prop: "name", label: "名称" },
  { type: "select", prop: "status", label: "状态", dictType: "sys_status" },
]);

const formConfig = defineFormConfig([
  { type: "input", prop: "name", label: "名称", required: true },
  { type: "select", prop: "status", label: "状态", dictType: "sys_status" },
]);

const columnsConfig = defineColumnsConfig([
  { type: "seq", title: "序号", width: 60 },
  { field: "name", title: "名称", minWidth: 120 },
  {
    field: "status",
    title: "状态",
    dictType: "sys_status",
    displayType: "tag",
  },
]);

useState({
  api: {
    list: (params) => getList(params),
    add: (data) => addItem(data),
    edit: (data) => updateItem(data),
    delete: (id) => deleteItem(id),
  },
  searchConfig,
  formConfig,
  columnsConfig,
  tableOptions: {
    operateColumns: [
      {
        title: "编辑",
        onClick: (row) => pageTemplateRef.value?.handleEdit(row),
      },
      {
        title: "删除",
        onClick: (row) => pageTemplateRef.value?.handleDelete(row),
      },
    ],
  },
});
</script>

<template>
  <PageTemplate ref="pageTemplateRef">
    <template #table>
      <Table />
    </template>
  </PageTemplate>
</template>
```

## 📚 核心功能

### 表单控件

内置 14 种表单控件，通过 `type` 属性指定：

| 类型                | 说明         | 类型           | 说明         |
| ------------------- | ------------ | -------------- | ------------ |
| `input`             | 输入框       | `textarea`     | 多行输入     |
| `number`            | 数字输入     | `select`       | 下拉选择     |
| `radio`             | 单选         | `date`         | 日期选择     |
| `time`              | 时间选择     | `upload`       | 文件上传     |
| `uploadCard`        | 卡片上传     | `editor`       | 富文本编辑器 |
| `line`              | 分割线       | `inputGroup`   | 输入组       |
| `addableInputGroup` | 可增删输入组 | `selectChange` | 搜索选择     |

### 字典管理

```typescript
import { useDict, useDictLabel, preloadDicts } from "vue-admin-kit";

// 响应式获取字典
const { dictData, loaded } = useDict("sys_status");

// 获取字典标签
const label = useDictLabel("sys_status", "1");

// 预加载字典
await preloadDicts(["sys_status", "sys_yes_no"]);
```

### 配置工具

采用"注册-使用"模式，支持配置复用：

```typescript
import { col, registerColumnConfigs, getColumnConfig } from "vue-admin-kit";

// 注册
registerColumnConfigs([
  col("序号", { type: "seq", width: 60 }),
  col("状态", { field: "status", dictType: "sys_status", displayType: "tag" }),
]);

// 使用
const columns = getColumnConfig(["序号", "状态", ["名称", { minWidth: 200 }]]);
```

### 权限指令

```vue
<template>
  <el-button v-hasPermi="['system:user:add']">新增</el-button>
  <el-button v-hasRole="['admin']">管理员操作</el-button>
</template>
```

## 📖 文档

完整文档请访问：**[https://vue-admin-kit.jiang.in/](https://vue-admin-kit.jiang.in/)**

- [快速开始](https://vue-admin-kit.jiang.in/guide/getting-started.html)
- [配置驱动](https://vue-admin-kit.jiang.in/guide/config-driven.html)
- [字典管理](https://vue-admin-kit.jiang.in/guide/dict-management.html)
- [表单控件](https://vue-admin-kit.jiang.in/components/form-controls.html)
- [API 参考](https://vue-admin-kit.jiang.in/api/types.html)

## 🔗 相关链接

- [GitHub](https://github.com/nicekate/vue-admin-kit)
- [更新日志](https://vue-admin-kit.jiang.in/changelog.html)
- [Element Plus](https://element-plus.org/)
- [VXE-Table](https://vxetable.cn/)

## 📄 License

[MIT](./LICENSE) © 2025 vue-admin-kit
