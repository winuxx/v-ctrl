<template>
  <!-- <h1>{{ msg }}</h1> -->

  <div class="select"
    v-bind:id="selectElId"
    v-on:click="onSelectClick"
    v-on:mouseenter="onSelectEnter"
    v-on:mouseleave="onSelectLeave"
    v-bind:style="primaryStyle"
  >
    <div class="label"
      v-bind:style="labelStyle"
    >
      {{ label }}
    </div>
    <div class="options"
      v-bind:class="{ expanded: isOptionsExpanded, unexpanded: !isOptionsExpanded }"
      v-on:mouseover="onOptionOver"
      v-on:mouseleave="onOptionLeave"
    >
      <div class="option fixed"
        v-bind:class="{ highlight: isSelectOver }"
      >
        <div class="text">
          {{ options[selectedIndex].text }}
        </div>
        <div class="icon right index"
            v-bind:class="{ hidden: !isOptionsExpanded }"
        >
          {{ selectedIndex + 1 }}
        </div>
      </div>
      <template
        v-for="(option, index) in options"
        v-bind:key="option"
      >
        <div class="option"
          v-bind:class="{ selected: selectedIndex == index, hidden: !isOptionsExpanded }"
          v-on:click="onOptionClick($event, option, index)"
        >
          <div class="text">
            {{ option.text }}
          </div>
          <div class="icon right index"
            v-bind:class="{ hidden: selectedIndex == index }"
          >
            {{ index + 1 }}
          </div>
          <div class="icon right selected"
            v-bind:class="{ hidden: !(selectedIndex == index), highlight: isSelectedOptionOver }"
          >
            <svg width="auto" height="auto" viewBox="0 0 100 100">
              <circle id="circle" cx="50" cy="50" r="50" />
            </svg>
          </div>
        </div>
      </template>
    </div>
    <div class="icon right expand"
      v-bind:class="{ hidden: isOptionsExpanded, highlight: isSelectOver }"
    >
      <svg width="auto" height="auto" viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
        <path id="arrow-down" stroke-width="0" stroke="#000" fill="" transform="rotate(90, 100, 100)" d="m100,100 -100,-100 l200,100 -200,100 l100,-100 z"/>
      </svg>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent, PropType, CSSProperties } from 'vue'

interface SelectOption {
  text: String,
  value: any,
  selected?: any,
}

// todo: should use store / storage
let id = 0;

export default defineComponent({
  name: 'Select',
  props: {
    msg: {
      type: String,
      required: false
    },
    label: String,
    options: {
      type: Array as PropType<SelectOption[]>,
      required: true,
      default: () => [
        {
          text: '',
          value: null,
          selected: undefined,
        }
      ]
    },
    primaryColor: {
      type: String,
      default: () => '#3ca576',
    }
  },
  emits: ['change'],
  data() {
    return {
      selectElId: '',
      isOptionsExpanded: false,
      isSelectOver: false,
      isSelectedOptionOver: false,
      selectedIndex: 0,
      labelPaddingTop: 0,
      primaryStyle: {
        '--primary-color': this.primaryColor,
      } as CSSProperties,
      labelStyle: {
        paddingTop: '3px',
      },
    }
  },
  setup: () => {},
  beforeMount () {
    id += 1;
    this.selectElId = 'select-ctrl-' + id;
    this.setDefaultSelectedOption();
  },
  mounted() {
    // console.log('name:', name);

    // todo: 可优化, select 数量多时绑定事件会过多
    window.addEventListener('click', this.onWindowClick);
  },
  unmounted() {
    window.removeEventListener('click', this.onWindowClick);
  },
  watch: {
    labelPaddingTop: function(val: number) {
      this.labelStyle['paddingTop'] = val + 3 + 'px';
    }
  },
  methods: {
    onWindowClick(event: Event) {
      console.log('on page click');
      // console.log(event);
      // console.log(window.event);

      // 点击页面上非当前 select 的地方, 收起 options
      // todo: 应该在事件传播到 select div 时记录其 id
      let selectElId = this.getSelectElId(event.target);
      if ( selectElId !== this.selectElId) {
        this.isOptionsExpanded = false;
      }
      this.labelPaddingTop = 0;
    },

    onSelectClick(event: Event) {
      console.log('on select click');
      this.isOptionsExpanded = !this.isOptionsExpanded;
    },

    onOptionClick(event: Event, option: SelectOption, index: number) {
      console.log('on option click');
      // 如果 options 未展开, 则冒泡到 onWindowClick 去处理
      // 如果阻止冒泡, 事件不会到达 window, 则点击一个 select 无法让其它的 select 收起
      if (!this.isOptionsExpanded) {
        return;
      }
      // 阻止冒泡 / 传递
      event.stopPropagation();
      event.cancelBubble = true;
      // 修改选定 option
      this.selectedIndex = index;
      // 还原 label 位置
      this.labelPaddingTop = 0;
      // 收起 options
      this.isOptionsExpanded = false;
      // 将选定 option 的值传给父组件
      this.$emit('change', option.value);
    },

    onSelectEnter(event: Event) {
      // console.log('on select enter');
      this.isSelectOver = true;
    },

    onSelectLeave(event: Event) {
      // console.log('on select leave');
      this.isSelectOver = false;
      // 鼠标移出后还原 label 位置
      this.labelPaddingTop = 0;
    },

    onOptionOver(event: Event) {
      // console.log('on option over', event);
      let els = this.getOptionAndParentEl(event.target);
      if (els === null) {
        return;
      }
      let [target, parent] = els;
      this.labelPaddingTop = target.offsetTop - parent.offsetTop;
      this.isSelectedOptionOver = target.classList.contains('selected') ? true : false;
    },

    onOptionLeave(event: Event) {
      // console.log('on option leave');
      // 鼠标移出后还原 label 位置
      // 当鼠标从 options 移动至 label 时, 或2个元素之间有间隔时, 若还原, 稍显突兀
      // 移至 select mouse leave
        this.isSelectedOptionOver = false;
    },

    setDefaultSelectedOption() {
      let i = 0;
      for (let option of this.options) {
        if (option.selected) {
          // selectedIndex 赋值应该在循环内. 若父组件没有设置 selected, 则 selectedIndex 保持默认值 (0)
          this.selectedIndex = i;
          break;
        }
        i += 1;
      }
    },

    getSelectElId(target: any): any {
      let elId = target.id;
      if (elId.indexOf('select') === 0) {
        return elId;
      }
      let parent = target.parentElement;
      if (!parent) {
        return null;
      }
      else {
        return this.getSelectElId(target.parentElement);
      }
    },

    getOptionAndParentEl(target: any): any {
      // console.log((<HTMLElement>target).classList);
      if (target === null) {
        return null;
      }
      let parent = (<HTMLElement>target).parentElement;
      if (parent === null) {
        return null;
      }
      if ((<HTMLElement>target).classList.contains('option')) {
        return [target, parent];
      }
      else {
        return this.getOptionAndParentEl(parent);
      }
    },
  }
})
</script>

<style scoped>
:root {
  /* --label-font-size: 16; */
  /* --option-font-size: 20; */
  /* --primary-color: #3ca576; */
}

/* common styles */
.hidden {
  display: none !important;
}
.highlight {
  color:#fff !important;
  background: var(--primary-color) !important;
}
.highlight > svg {
  fill: #fff !important;
}

a {
  color: var(--primary-color);
}

.select {
  /* background: var(--primary-color); */
  width: auto;
  min-width: 2rem;
  max-width: 100%;
  display: flex;
  /* align-items: center; */
  border:var(--primary-color) 1px solid;
  border-radius: 0.2em;
}
.label {
  color: #fff;
  background: var(--primary-color);
  font-size: 16px;
  text-align: right;
  white-space: nowrap;
  padding-left: 0.5em;
  padding-right: 0.5em;
  /* padding-top: v-bind(labelPaddingTop + 3 + "px"); */
  border-right: 1px solid;
}
.options {
  width: 100%;
}
.option {
  /* width: 100%; */
  display: flex;
  align-items: center;
  font-size: 20px;
}
.option.selected {
  color: var(--primary-color);
  /* font-weight: bold; */
}
.option.fixed {
  color:var(--primary-color);
}
.expanded > .option.fixed {
  color:#fff;
  background: var(--primary-color);
  border-bottom: 1px solid;
}
.option:hover {
  color: #fff;
  background: var(--primary-color);
  /* height: 1.5em; */
  /* font-weight: bold; */
}
.text {
  width: 100%;
  padding: 0 0.5em;
  text-align: left;
}
.expanded > .option > .text {
  border-right: 1px solid #fff;
}
.icon {
  flex-shrink: 0;
  width: 2rem;
}
.icon.right {
  float: right;
}
.icon.expand {
  color: var(--primary-color);
  padding-top: 2px;
  border-left: 1px solid;
}
.icon.index {
  font-size: 12px;
}
.icon.selected {
  font-size: 16px;
  line-height: 16px;
}
.icon > svg {
  height: 12px;
  fill: var(--primary-color);
}
.icon > img {
  height: 1rem;
}
</style>
