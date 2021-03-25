<template>
  <!-- <h1>{{ msg }}</h1> -->

  <div class="select"
    v-bind:id="id"
    v-bind:class="{ expanded: expanded, unexpanded: !expanded }"
    v-on:click="onSelectClick"
  >
    <div class="label"
      v-on:click="onLabelClick"
    >
      {{ label }}
    </div>
    <div class="options"
      v-on:mouseover="onOptionOver"
      v-on:mouseout="onOptionOut"
    >
      <template
        v-for="(option, index) in options"
        v-bind:key="option"
      >
        <div class="option"
          v-bind:class="{ selected: option.selected, hidden: !expanded && !option.selected }"
          v-on:click="onOptionClick($event, option, index)"
        >
          {{ option.text }}
        </div>
      </template>
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
  data() {
    return {
      expanded: false,
      id: '',
      selectedValue: undefined,
      selectedIndex: 0,
      hoverElementOffsetTop: 0,
    }
  },
  setup: () => {
  },
  mounted() {
    // console.log('name:', name);
    // console.log('data:', this.data);

    // todo: 可优化, select 数量多时绑定事件会过多
    window.addEventListener('click', this.onWindowClick);
    id += 1;
    this.id = 'select-ctrl-' + id;
    // console.log('id:', id);
  },
  methods: {
    onWindowClick(event: Event) {
      console.log('on page click');
      // console.log(event);

      // 点击页面上非当前 select 的地方, 收起 options
      let target = event.target;
      // console.log(target);
      // todo: 应该在事件传播到 select div 时记录其 id
      let id = target.id || target.parentNode.id || (target.parentNode.parentNode &&target.parentNode.parentNode.id);
      if ( id != this.id) {
        this.expanded = false;
      }
      this.hoverElementOffsetTop = 0;
    },

    onSelectClick(event: Event) {
      console.log('on select click');
      // event.stopPropagation();
      // event.cancelBubble = true;
      this.expanded = !this.expanded;
    },

    onLabelClick(event: Event) {
      console.log('on label click');
      // console.log(event);
    },

    onOptionClick(event: Event, option: Object, index: Number) {
      console.log('on option click');
      // 如果 options 未展开, 则冒泡到 onWindowClick 去处理
      // 如果阻止冒泡, 事件不会到达 window, 则点击一个 select 无法让其它的 select 收起
      if (!this.expanded) {
        return;
      }
      // 阻止冒泡 / 传递
      event.stopPropagation();
      event.cancelBubble = true;
      this.expanded = !this.expanded;
      // select option
      this.selectedIndex = index;
      this.selectedValue = option.valueOf().value;
      console.log('selected option:', index, this.selectedValue);
      this.changeSelected(index);
    },

    onOptionOver(event: Event) {
      // console.log('on option over');
      // console.log(event.target);
      let target = event.target;
      let offsetTop = target.offsetTop - target.parentElement.offsetTop;
      console.log(offsetTop);
      this.hoverElementOffsetTop = offsetTop;
    },

    onOptionOut(event: Event) {
      // console.log('on option out');
      // 鼠标移出后还原 label 位置, 会比较突兀
      // 移至 window click 事件处理中
      // this.hoverElementOffsetTop = 0;
    },

    changeSelected(index: Number) {
      let i = 0
      for (let option of this.options) {
        if (i != index && option.hasOwnProperty('selected')) {
          delete this.options[i].selected;
        }
        i += 1;
      }
      this.options[index].selected = true;
      // console.log('options:', this.options);
    }
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

/* use id */
.select.unexpanded:after {
  color: white;
  background: #3ca576;
  width: 2em;
  content: "+";
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
}

.options {
  width: 100%;
}

.option {
  /* width: 100%; */
  font-size: 20px;
  padding-left: 0.5em;
  padding-right: 0.5em;
}

.option.selected {
  /* background: #3ca576; */
  color: #3ca576;
  /* font-weight: bold; */
}

.option.hidden {
  display: none;
}

.option:hover {
  color: white;
  background: #3ca576;
  /* height: 1.5em; */
  /* font-weight: bold; */
}
</style>
