<template>
  <div ref="container" class="select">
    <input
      type="text"
      class="select__input"
      :class="inputClass"
      :style="inputStyle"
      :placeholder="placeholder"
      :tabindex="!disabled ? tabindex : -1"
      :disabled="disabled"
      v-model="inputValue"
      @input="onInput"
      @focus="onFocus"
      @blur="onBlur"
      @keydown="onKeyDown"
      @change="onChange"
    />

    <div v-if="overlayVisible" ref="overlayRef" class="select__options">
      <ul ref="optionsContainer" class="select__items" role="listbox">
        <li
          v-for="(option, i) in autocomplete"
          :key="option.value"
          class="select__item"
          :class="{ focus: focusedOptionIndex === i }"
          role="option"
          :aria-label="option.label"
          tabindex="-1"
          @click="onOptionSelect($event, option)"
          @keydown.enter="add(option)"
        >
          {{ option.label }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "MySelect",
  emits: [
    "update:inputValue",
    "change",
    "focus",
    "blur",
    "item-select",
    "clear",
    "show",
    "hide",
    "DownKey-click",
    "UpKey-click",
    "EnterKey-click"
  ],
  props: {
    suggestions: {
      type: Array,
      default: null
    },
    inputStyle: {
      type: null,
      default: null
    },
    inputClass: {
      type: String,
      default: null
    },
    disabled: {
      type: Boolean,
      default: false
    },
    placeholder: {
      type: String,
      default: null
    },
    tabindex: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      inputValue: "",
      select: null,
      autocomplete: [],
      overlayVisible: false,
      focusedOptionIndex: 0
    };
  },
  methods: {
    onInput(event) {
      setTimeout(() => {
        if (!this.inputValue.length) {
          this.autocomplete = [];
        } else {
          this.autocomplete = this.suggestions.filter((option) => {
            return option.label
              .toLowerCase()
              .startsWith(this.inputValue.toLowerCase());
          });
        }

        this.show();
      }, 150);

      if (this.inputValue === "") {
        this.$emit("clear", event);
      }
    },
    onFocus(event) {
      this.$emit("focus", event);
    },
    onBlur(event) {
      this.$emit("blur", event);
    },
    onKeyDown(event) {
      switch (event.code) {
        case "ArrowDown":
          this.onDownKey(event);
          break;
        case "ArrowUp":
          this.onUpKey(event);
          break;
        case "PageDown":
          this.onDownKey(event);
          break;
        case "PageUp":
          this.onUpKey(event);
          break;
        case "Enter":
          this.onEnterKey(event);
          break;
        default:
          break;
      }
    },
    onChange(event) {
      this.$emit("change", event);

      if (this.inputValue === "") {
        this.$emit("clear", event);
      }
    },
    onDownKey(event) {
      this.$emit("DownKey-click", event);
      event.preventDefault();
      if (this.focusedOptionIndex < this.autocomplete.length - 1) {
        this.focusedOptionIndex++;
      }
      this.scroll("down");
    },
    onUpKey(event) {
      this.$emit("UpKey-click", event);
      event.preventDefault();
      if (this.focusedOptionIndex > 0) {
        this.focusedOptionIndex--;
      }
      this.scroll("up");
    },
    onEnterKey(event) {
      this.$emit("EnterKey-click", event);
      const elements = this.$refs.optionsContainer;
      for (let node of elements.childNodes) {
        if (node.classList !== undefined && node.classList.contains("focus")) {
          node.click();
          return;
        }
      }
    },
    onOptionSelect(event, option) {
      this.add(option);
      this.$emit("item-select", { originalEvent: event, value: option });
    },
    show() {
      this.$emit("show");
      if (this.inputValue && this.autocomplete.length) {
        this.overlayVisible = true;
      } else {
        this.hide();
      }
    },
    hide() {
      const _hide = () => {
        this.$emit("hide");
        this.overlayVisible = false;
        this.focusedOptionIndex = 0;
      };
      setTimeout(() => {
        _hide();
      }, 0);
    },
    add(option) {
      this.inputValue = option.label;
      this.select = option;
      this.hide();
    },
    scroll(direction) {
      const overlay = this.$refs.overlayRef;
      if (overlay === undefined) {
        return;
      }

      const elements = this.$refs.optionsContainer;
      for (let node of elements.childNodes) {
        if (node.classList !== undefined && node.classList.contains("focus")) {
          switch (direction) {
            case "up":
              overlay.scrollTop = node.offsetTop - node.scrollHeight;
              break;
            case "down":
              overlay.scrollTop =
                node.offsetTop - node.scrollHeight - overlay.offsetTop;
              break;
            default:
              break;
          }

          return;
        }
      }
    }
  },
  mounted() {
    document.addEventListener("click", this.hide.bind(this));
  },
  unmounted() {
    document.removeEventListener("click", this.hide.bind(this));
  }
};
</script>

<style>
.select {
  position: relative;
  box-sizing: border-box;
}

.select__input {
  min-width: 100%;
  padding: 0.75rem 0.75rem;
  margin: 0;
  box-sizing: border-box;
  border: 1px solid #ced4da;
  font-size: 1rem;
  color: #495057;
  background: #ffffff;
  transition: background-color 0.2s, color 0.2s, border-color 0.2s,
    box-shadow 0.2s;
  appearance: none;
  border-radius: 6px;
}

.select__input:enabled:focus {
  outline: 0 none;
  outline-offset: 0;
  box-shadow: 0 0 0 0.2rem #bfdbfe;
  border-color: #3b82f6;
}

.select__input:enabled:hover {
  border-color: #3b82f6;
}

.select__options {
  position: absolute;
  box-sizing: border-box;
  border: 1px solid rgb(219, 223, 223);
  margin-top: 4px;
  border-radius: 4px;
  min-width: 100%;
  overflow: auto;
  left: 0;
  max-height: 200px;
  z-index: 1001;
  transform-origin: center top;
}

.select__items {
  margin: 0;
  padding: 0;
  list-style-type: none;
}

.select__item {
  cursor: pointer;
  white-space: nowrap;
  position: relative;
  overflow: hidden;
  margin: 0;
  padding: 0.75rem 1.25rem;
  border: 0 none;
  color: #495057;
  background: transparent;
  transition: box-shadow 0.2s;
}

.select__items .select__item:hover,
.select__items .select__item:focus,
.focus {
  color: #495057;
  background: #e9ecef;
  border: none;
  outline: none;
}
</style>
