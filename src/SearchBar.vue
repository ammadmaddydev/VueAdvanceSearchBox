<template>
    <div class="searchWrapper" :class="addClass" v-click-outside-app="hide">
      <div class="searchFieldWrapper">
        <input type="text" class="searchTerm" :placeholder="placeholder" :value="modelValue"
               v-on:keyup.enter="searchEnter" v-on:keyup="searchOnType($event)" @focus="focusInput(1)"
               :disabled="disabled">
        <button class="icoBtn" v-if="isSearching && iconLoader!=''">
          <i :class="iconLoader"></i>
        </button>
        <template v-else>
          <button type="button" @click="cancelSearch()" class="icoBtn" v-if="modelValue!='' && iconCancel!=''">
            <i :class="iconCancel"></i>
          </button>
        </template>
        <button type="submit" class="searchButton" v-if="iconSearch!=''">
          <i :class="iconSearch"></i>
        </button>
      </div>
      <template v-if="showOption">
        <template v-if="searchResults.length==0 && modelValue!='' && isSearching==false">
          <div class="searchItems">
            <div class="items">{{ searchNotFound }}</div>
          </div>
        </template>
        <template v-else>
          <div v-bind:class="{'no-border': searchResults.length==0}" class="searchItems">
            <template v-for="(list,index) in searchResults" :key="index">
              <div class="items" @click="selectOption(list.value, list.text)"><span
                  v-html="highlightWords(list.text)"></span></div>
            </template>
          </div>
        </template>
      </template>
    </div>
</template>
<script>
export default {
  name: 'SearchBar',
  props: {
    placeholder: {
      type: String,
      default: 'What are you looking for ?'
    },
    modelValue: String,
    disabled: {
      type: Boolean,
      default: false,
    },
    addClass: {
      type: String,
      default: '',
    },
    searchResults: Array,
    isSearching: {
      type: Boolean,
      default: false,
    },
    onSelectHideList:{
      type: Boolean,
      default: false,
    },
    searchNotFound: {
      type: String,
      default: 'Not Found.',
    },
    iconSearch: {
      type: String,
      default: '',
    },
    iconCancel: {
      type: String,
      default: '',
    },
    iconLoader: {
      type: String,
      default: '',
    }
  },
  data() {
    return {
      showOption: 0,
    }
  },
  methods: {
    searchEnter() {
      this.$emit('enter-press');
    },
    searchOnType(e) {
      clearTimeout(this.timeout);
      var self = this;
      this.timeout = setTimeout(function () {
        self.$emit('update:modelValue', e.target.value);
        self.$emit('on-typing');
      }, 250);
    },
    cancelSearch() {
      this.$emit('update:modelValue', '');
      this.focusInput(0);
    },
    selectOption(value, text) {
      this.$emit('update:modelValue', text);
      this.$emit('option-select', value, text);
      if(this.onSelectHideList){
        this.showOption = 0;
      }
    },
    focusInput(focus) {
      this.showOption = focus;
    },
    hide() {
      this.showOption = 0;
    },
    highlightWords(text) {
      const substring = new RegExp(this.modelValue, "gi"); // "g" for global, "i" for case-insensitive
      return text.toString().replace(substring, function (matchedText) {
        return ('<span class=\'wordHighlight\'>' + matchedText + '</span>');
      });
    }
  },
  directives: {
    "click-outside-app": {
      beforeMount: (el, binding) => {
        el.clickOutsideEvent = event => {
          // here I check that click was outside the el and his children
          if (!(el == event.target || el.contains(event.target))) {
            // and if it did, call method provided in attribute value
            binding.value();
          }
        };
        document.addEventListener("click", el.clickOutsideEvent);
      },
      unmounted: el => {
        document.removeEventListener("click", el.clickOutsideEvent);
      },
    }
  },
}
</script>
<style lang="scss">
.searchFieldWrapper {
  width: 100%;
  height: 30px;
  position: relative;
  display: flex;
  border: 3px solid #2c3e50;
  border-radius: 5px;
}

.searchFieldWrapper .searchTerm {
  width: 100%;
  height: auto;
  border: none;
  padding: 5px;
  outline: none;
  font-size: 14px;
}

.searchFieldWrapper .searchTerm:focus {
  color: #000;
}

.searchFieldWrapper .searchButton {
  width: 40px;
  height: auto;
  border: none;
  background: none;
  text-align: center;
  color: #000;
  cursor: pointer;
  font-size: 20px;
  padding: 5px;
  outline: none;
}

.searchFieldWrapper .icoBtn {
  width: 40px;
  height: auto;
  border: none;
  background: none;
  text-align: center;
  color: #000;
  cursor: pointer;
  font-size: 20px;
  padding: 5px;
  outline: none;
}

.searchWrapper .searchItems {
  width: 100%;
  box-shadow: 0px 1px 5px 0px grey;
  border: 3px solid #80808073;
  border-top: none;
  border-radius: 5px;
  margin-top: 2px;
  max-height: 350px;
  overflow-y: auto;
}

.searchWrapper .searchItems .items {
  text-align: left;
  padding: 7px;
  border-bottom: 1px solid #80808033;
}

.searchWrapper .items:hover {
  background: #efefef;
  cursor: default;
}

.searchWrapper .wordHighlight {
  font-weight: bold;
}

.searchWrapper .no-border {
  border: none;
}
</style>
