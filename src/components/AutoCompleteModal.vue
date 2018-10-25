<template>
  <div class="auto-complete" @keydown.38="moveSelection" @keydown.40="moveSelection">
    <ul>
      <li
          v-for="(keyword, index) in filteredKeywords"
          :style="{backgroundColor: `${selectedIndex === index ? 'gray' : 'transparent'}`}"
          @click="onSelect"
      >
        <span :style="{backgroundColor: `${keyword.color}`, whiteSpace: 'noWrap'}">{{keyword.key}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'AutoCompleteModal',
  props: {
    filteredKeywords: Array,
    selectedIndex: Number
  },
  methods: {
    onSelect (event) {
      this.$emit('autoComplete', event.target.innerText);
    }
  }
}
</script>

<style scoped>
  ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  ul li {
    color: white;
    padding: 5px 10px;
  }

  .auto-complete {
    max-width: 200px;
    padding: 0;
    border: 1px solid gray;
    background-color: white;
  }
</style>
