<template>
  <div ref="container" class="select">
    <input type="text" class="select__input" v-bind="inputProps" />

    <div class="select__options">
      <ul id="pv_id_3_list" class="select__items" role="listbox">
        <li
          v-for="(option, i) in suggestions"
          :key="i"
          class="select__item"
          role="option"
          :aria-label="option.label"
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
  props: {
    suggestions: {
      type: Array,
      default: null,
    },
  },
  data() {
    return {
      inputProps: null,
    };
  },
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

.select__items .select__item:hover {
  color: #495057;
  background: #e9ecef;
}
</style>
