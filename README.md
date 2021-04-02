# Vue Select

A select control based on Vue 3

## How to run

```command
npm install -g serve
serve -s dist
```

## Features

* 鼠标悬停项高亮 / 阴影
    * 文字 / 背景高亮
    * 增大高度
* 点击后选择框会上下展开, 而不是下拉
    * 向下展开
    * 上下展开, 保持选中项在页面位置不变
    * 展开动作可设置一些延迟, 防止双击误选
* 选中项高亮
    * 文字 / 背景高亮
    * 选中项可增加图标或符号标识 (勾)
    * 若鼠标未悬停在 select 上, 则增大选中项高度
* 鼠标移动时, label 跟随移动, 与当前 hover 项对齐
* 多个 select 纵向排列, 宽度应该保持一致, 包括 label 和 options
    * 由父组件配置
* 点击控件展开时, 鼠标指向第一项, 容易误选 (常规逻辑: 点击一次展开, 再点击一次收起)
    * label 放在上方
    * 选中项移动至第一个
    * 选中项复制到第一个
    * 上下展开, 选中项在正中间, 保持位置不变
        * option 需悬浮显示
        * 靠近页面顶部时, 依然保证选中项在当前位置, 往下滚动时则加长选框
* 点击页面上非当前 select 控件处收起 options
    * 包括点击其它 select 控件
* 右边图标, 展开 / 收起
* 事件绑定到 select 或 options 上, 避免 option 过多时, 绑定事件过多
* label 为空时, 不显示 label
* option 为空
    * disable select?
        * 有默认值, 判断较麻烦
    * 无意义, 故不考虑
* css 变量抽取

### Todo

* 自动宽度
* 当 option 很多, 滚动到超出选中项时, 选中项依然展示
    * 选中项在上方时, 展示在顶部
    * 选中项在下方时, 展示在底部
* 选项很多需要滚动时, 每次滚动一格
* overflow
    * options 使用 scroll (auto)
    * label 动态位置可能会偏移, 需要修改
* primary-color 改成即时(动态绑定)?
    * primaryStyle 在 data 里, 不会即时改变
* 键盘操作
    * Tab, Enter

## Issue

* css v-bind 失效
    * style 未添加到 select 节点
    * 原因未知
