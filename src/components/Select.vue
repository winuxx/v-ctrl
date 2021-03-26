<template>
  <!-- <h1>{{ msg }}</h1> -->

  <div class="select"
    v-bind:id="id"
    v-on:click="onSelectClick"
  >
    <div class="label"
      v-on:click="onLabelClick"
    >
      {{ label }}
    </div>
    <div class="options"
      v-bind:class="{ expanded: isOptionsExpanded, unexpanded: !isOptionsExpanded }"
      v-on:mouseover="onOptionOver"
      v-on:mouseout="onOptionOut"
    >
      <div class="option fixed"
        v-bind:class="{ hidden: !isOptionsExpanded }"
      >
        <div class="option-text">
          {{ options[selectedIndex].text }}
        </div>
        <div class="icon right index">
          {{ selectedIndex + 1 }}
        </div>
      </div>
      <template
        v-for="(option, index) in options"
        v-bind:key="option"
      >
        <div class="option"
          v-bind:class="{ selected: option.selected, hidden: !isOptionsExpanded && !option.selected }"
          v-on:click="onOptionClick($event, option, index)"
        >
          <!-- 不能使用span, 无法捕获鼠标悬停事件 -->
          <!-- <span>{{ option.text }}</span> -->
          <div class="option-text">
            {{ option.text }}
          </div>
          <div class="icon right index"
            v-bind:class="{ hidden: !isOptionsExpanded || option.selected }"
          >
            {{ index + 1 }}
          </div>
          <div class="icon right selected"
            v-bind:class="{ hidden: !isOptionsExpanded || !option.selected }"
          >
            S
          </div>
        </div>
      </template>
    </div>
    <div class="icon right expand"
      v-bind:class="{ hidden: isOptionsExpanded, highlight: isOptionOver }"
    >
      +
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent } from 'vue'

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
      type: Array,
      default: () => [
        {
          text: '',
          value: null,
          selected: undefined
        }
      ]
    },
  },
  emits: ['change'],
  data() {
    return {
      id: '',
      isOptionsExpanded: false,
      isOptionOver: false,
      selectedValue: undefined,
      selectedIndex: 0,
      hoverElementOffsetTop: 0,
    }
  },
  setup: () => {},
  mounted() {
    // console.log('name:', name);
    // console.log('data:', this.data);

    // todo: 可优化, select 数量多时绑定事件会过多
    window.addEventListener('click', this.onWindowClick);
    id += 1;
    this.id = 'select-ctrl-' + id;
    // console.log('id:', id);
    this.getSelectedOption();
  },
  methods: {
    onWindowClick(event: Event) {
      console.log('on page click');
      // console.log(event);
      // console.log(window.event);

      // 点击页面上非当前 select 的地方, 收起 options
      let target = event.target;
      // console.log(target);
      // todo: 应该在事件传播到 select div 时记录其 id
      let id = this.getSelectElId(target);
      if ( id !== this.id) {
        this.isOptionsExpanded = false;
      }
      this.hoverElementOffsetTop = 0;
    },

    onSelectClick(event: Event) {
      console.log('on select click');
      // event.stopPropagation();
      // event.cancelBubble = true;
      this.isOptionsExpanded = !this.isOptionsExpanded;
    },

    onLabelClick(event: Event) {
      console.log('on label click');
      // console.log(event);
    },

    onOptionClick(event: Event, option: Object, index: Number) {
      console.log('on option click');
      // 如果 options 未展开, 则冒泡到 onWindowClick 去处理
      // 如果阻止冒泡, 事件不会到达 window, 则点击一个 select 无法让其它的 select 收起
      if (!this.isOptionsExpanded) {
        return;
      }
      // 阻止冒泡 / 传递
      event.stopPropagation();
      event.cancelBubble = true;
      this.isOptionsExpanded = !this.isOptionsExpanded;
      // select option
      this.selectedIndex = index;
      let selectedValue = option.valueOf().value;
      this.$emit('change', selectedValue);
      this.changeSelectedOption(index);
      this.hoverElementOffsetTop = 0;
    },

    onOptionOver(event: Event) {
      // console.log('on option over');
      // console.log(event.target);
      // let target = event.target;
      let target = event.target.parentElement;
      let offsetTop = target.offsetTop - target.parentElement.offsetTop;
      // console.log(offsetTop);
      this.hoverElementOffsetTop = offsetTop;
      this.isOptionOver = true;
    },

    onOptionOut(event: Event) {
      // console.log('on option out');
      // 鼠标移出后还原 label 位置, 当鼠标从 options 移动至 label 时, 稍显突兀
      // 可移至 select mouse out 事件处理
      this.hoverElementOffsetTop = 0;
      this.isOptionOver = false;
    },

    changeSelectedOption(index: Number) {
      let i = 0
      for (let option of this.options) {
        if (i !== index && option.hasOwnProperty('selected')) {
          delete this.options[i].selected;
        }
        i += 1;
      }
      this.options[index].selected = true;
      // console.log('options:', this.options);
    },

    getSelectedOption() {
      let i = 0;
      for (let option of this.options) {
        if (option.selected) {
          this.selectedIndex = i;
          this.selectedValue = option.value;
          return;
        }
        i += 1;
      }
    },

    getSelectElId(target: Element) {
      let id = target.id;
      if (id.indexOf('select') === 0) {
        return id;
      }
      let parent = target.parentElement;
      if (!parent) {
        return null;
      }
      else {
        return this.getSelectElId(target.parentElement);
      }
    },
  }
})
</script>

<style scoped>
/* :root {
  --label-font-size: 16;
  --option-font-size: 20;
} */

a {
  color: #42b983;
}

label {
  margin: 0 0.5em;
  font-weight: bold;
}

.select {
  /* background: #42b983; */
  width: auto;
  min-width: 2rem;
  max-width: 100%;
  display: flex;
  /* align-items: center; */
  border:#42b983 1px solid;
  border-radius: 0.2em;
}

.hidden {
  display: none !important;
}

.label {
  color: white;
  background: #3ca576;
  /* width: 100%; */
  font-size: 16px;
  text-align: right;
  padding-left: 0.5em;
  padding-right: 0.5em;
  padding-top: v-bind(hoverElementOffsetTop + 2 + "px");
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
  /* background: #3ca576; */
  color: #3ca576;
  /* font-weight: bold; */
}

.option.fixed {
  color:#3ca576;
  border-bottom: 1px solid;
}

.option:hover {
  color: white;
  background: #3ca576;
  /* height: 1.5em; */
  /* font-weight: bold; */
  /* border-left: 1px solid; */
  /* border-right: 1px solid; */
}

.option-text {
  width: 100%;
  padding: 0 0.5em;
  text-align: left;
}

.icon {
  flex-shrink: 0;
  width: 2rem;
}

.icon.right {
  float: right;
}

.icon.expand {
  color: #3ca576;
  padding-top: 2px;
  border-left: 1px solid;
}

.icon.expand.highlight {
  color:white;
  background: #3ca576;
}

.icon.index {
  font-size: 12px;
}

.icon.selected {
  font-size: 16px;
}
</style>
