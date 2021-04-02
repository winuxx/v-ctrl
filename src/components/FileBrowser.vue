<template>
  <div class="file-browse"
    v-bind:id="fbElId"
    v-on:click="onFbClick"
    v-bind:style="primaryStyle"
  >
    <div class="label fb-label"
      v-bind:style="labelStyle"
      v-if="label != null"
    >
      <span>{{ label }}</span>
    </div>
    <input class="text fb-text" disabled="true" placeholder="Selected files"
      v-bind:class="{ expanded: isOptionsExpanded, unexpanded: !isOptionsExpanded }"
      v-on:mouseover="onOptionOver"
      v-on:mouseleave="onOptionLeave"
    >
    <!-- <input type="button" value="Browser"> -->
    <button class="fb-button">Browse</button>
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
  name: 'FileBrowser',
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
      fbElId: '',
      isOptionsExpanded: false,
      isSelectOver: false,
      isSelectedOptionOver: false,
      selectedIndex: 0,
      labelPaddingTop: 0,
      primaryStyle: {
        '--primary-color': this.primaryColor,
        '--shadow-color': this.primaryColor + '80',
      } as CSSProperties,
      labelStyle: {
        paddingTop: '3px',
      },
    }
  },
  watch: {
    labelPaddingTop: function(val: number) {
      this.labelStyle['paddingTop'] = val + 3 + 'px';
    }
  },
  setup: () => {},
  beforeMount () {
    id += 1;
    this.fbElId = 'fb-ctrl-' + id;
  },
  mounted() {
  },
  unmounted() {
  },
  methods: {
    onFbClick(event: Event) {
      console.log('on file-browse click');
    },
    getfbElId(target: any): any {
      let elId = target.id;
      if (elId.indexOf('fb-ctrl') === 0) {
        return elId;
      }
      let parent = target.parentElement;
      if (!parent) {
        return null;
      }
      else {
        return this.getfbElId(target.parentElement);
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

.file-browse {
  /* background: var(--primary-color); */
  width: auto;
  min-width: 2rem;
  max-width: 100%;
  display: flex;
  /* align-items: center; */
  border:var(--primary-color) 1px solid;
  border-radius: 0.2em;
}
.file-browse:hover,
.file-browse:focus {
  box-shadow: 0 0 1em var(--shadow-color);
}
.label {
  color: #fff;
  background: var(--primary-color);
  display: flex;
  align-items: center;
  font-size: 16px;
  white-space: nowrap;
  padding-left: 0.5em;
  padding-right: 0.5em;
  /* border-right: 1px solid; */
  user-select: none;
}
.fb-text {
  color: var(--primary-color);
  background: #fafafa;
  width: 100%;
  flex: 1;
  padding-left: 0.5em;
  padding-right: 0.5em;
  font-size: 20px;
  border: none;
  z-index: 10;
}
.fb-button {
  color: #fff;
  background: var(--primary-color);
  border: none;
}
.text {
  width: 100%;
  height: 100%;
  padding: 0 0.5em;
  text-align: left;
}
.icon {
  flex-shrink: 0;
  width: 2rem;
}
.icon.expand {
  color: var(--primary-color);
}
.icon.index {
  font-size: 12px;
}
.icon.selected {
  font-size: 16px;
  line-height: 16px;
}
.icon.svg {
  display: flex;
  align-items: center
}
.icon > svg {
  fill: var(--primary-color);
  flex: 1;
  height: 12px;
}
</style>
