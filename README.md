# Vue Select

This template should help get you started developing with Vue 3 and Typescript in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur). Make sure to enable `vetur.experimental.templateInterpolationService` in settings!

### If Using `<script setup>`

[`<script setup>`](https://github.com/vuejs/rfcs/pull/227) is a feature that is currently in RFC stage. To get proper IDE support for the syntax, use [Volar](https://marketplace.visualstudio.com/items?itemName=johnsoncodehk.volar) instead of Vetur (and disable Vetur).

## Type Support For `.vue` Imports in TS

Since TypeScript cannot handle type information for `.vue` imports, they are shimmed to be a generic Vue component type by default. In most cases this is fine if you don't really care about component prop types outside of templates. However, if you wish to get actual prop types in `.vue` imports (for example to get props validation when using manual `h(...)` calls), you can use the following:

### If Using Volar

Run `Volar: Switch TS Plugin on/off` from VSCode command palette.

### If Using Vetur

1. Install and add `@vuedx/typescript-plugin-vue` to the [plugins section](https://www.typescriptlang.org/tsconfig#plugins) in `tsconfig.json`
2. Delete `src/shims-vue.d.ts` as it is no longer needed to provide module info to Typescript
3. Open `src/main.ts` in VSCode
4. Open the VSCode command palette
5. Search and run "Select TypeScript version" -> "Use workspace version"

# Features / todo

* 点击后选择框会上下展开, 而不是下拉
    * 展开动作可设置一些延迟, 防止双击误选
* 选中项背景高亮, 并且增大高度, 如果选中项不是鼠标 hover, 则只高亮文字
    * 选中项可增加图标或符号标识 (勾)
* 鼠标 hover 项背景高亮, 并且增大高度
* 鼠标移动时, label 跟随移动, 与当前 hover 项对齐
* 多个 select 纵向排列, 宽度应该保持一致, 包括 label 和 options
* 点击页面上非当前 select 控件处收起 options
    * 双击才收起, 防止误触?
* 自动宽度
* css 变量抽取
* 展开 / 收起, 右边图标
