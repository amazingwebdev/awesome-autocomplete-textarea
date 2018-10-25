<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div style="position: relative">
      <div ref="updateHTML" class="update-html"></div>
      <textarea
        id="message"
        class="main-text-container"
        v-model="mainText"
        @input="onChangeText"
        @keydown.up="onKeyUp"
        @keydown.down="onKeyDown"
        @keydown.enter="onKeyEnter"
      ></textarea>
      <auto-complete-modal
        ref="autoCompleteModal"
        :filtered-keywords="filteredKeywords"
        :selected-index="selectedKeyIndex"
        @autoComplete="autoComplete"
        class="modal-wrapper"
        :style="{visibility: `${isVisibleModal ? 'visible' : 'hidden'}`}"
      ></auto-complete-modal>
    </div>
  </div>
</template>

<script>
import AutoCompleteModal from './components/AutoCompleteModal.vue';
import getCaretCoordinates from 'textarea-caret';

export default {
  name: 'app',
  components: {
    AutoCompleteModal
  },
  computed: {
    filteredKeywords () {
      this.selectedKeyIndex = -1;
      return this.keywords.filter(keyword => this.search.startsWith('{{') && keyword.key.toLowerCase().includes(this.search.toLowerCase()));
    }
  },
  data () {
    return {
      keywords: [
        {
          key: '{{First Name}}',
          color: 'blue'
        },
        {
          key: '{{Email}}',
          color: 'blue'
        },
        {
          key: '{{Gender2}}',
          color: 'green'
        },
        {
          key: '{{Age}}',
          color: 'green'
        }
      ],
      search: '',
      mainText: '',
      selectedKeyIndex: -1,
      isVisibleModal: false
    }
  },
  methods: {
    onChangeText (event) {
      const strings = this.mainText.split(' ');
      const endPosition = getCaretCoordinates(event.target, event.target.selectionEnd);

      this.search = strings[strings.length - 1];
      this.$refs.autoCompleteModal.$el.style.cssText = `position: absolute; left: ${endPosition.left}px; top: ${endPosition.top}px;`;

      if(!this.filteredKeywords.length || !this.mainText) {
        this.$refs.autoCompleteModal.$el.style.visibility = 'hidden';
      }

      this.updateHTML();
    },
    updateHTML () {
      let mainTextCopy = this.mainText;

      String.prototype.replaceAll = function(search, replacement) {
        let target = this;
        return target.replace(new RegExp(search, 'g'), replacement);
      };

      this.keywords.forEach(keyword => {
        const replacement = `<span style="background-color: ${keyword.color}; color: white;">${keyword.key}</span>`;

        mainTextCopy = mainTextCopy.replaceAll(keyword.key, replacement);
      });

      this.$refs.updateHTML.innerHTML = mainTextCopy;
    },
    autoComplete (selectedText) {
      const lastReplacePosition = this.mainText.lastIndexOf(this.search);

      this.mainText = this.mainText.substring(0, lastReplacePosition) + selectedText;
      this.$refs.autoCompleteModal.$el.style.visibility = 'hidden';
      document.getElementById('message').focus();

      this.updateHTML();
    },
    onKeyUp (event) {
      if (this.filteredKeywords.length) {
        event.preventDefault();

        switch (this.selectedKeyIndex) {
          case 0:
          case -1:
            this.selectedKeyIndex = this.filteredKeywords.length - 1;
            break;
          default:
            this.selectedKeyIndex = this.selectedKeyIndex - 1;
        }
      }
    },
    onKeyDown (event) {
      if (this.filteredKeywords.length) {
        event.preventDefault();

        this.selectedKeyIndex = (this.selectedKeyIndex + 1) % this.filteredKeywords.length;
      }
    },
    onKeyEnter (event) {
      if (this.selectedKeyIndex >= 0) {
        event.preventDefault();

        this.autoComplete(this.filteredKeywords[this.selectedKeyIndex].key);
        this.selectedKeyIndex = -1;

        this.updateHTML();
      }
    }
  },
  mounted () {
    this.$refs.autoCompleteModal.$el.style.visibility = 'hidden';
    document.getElementById('message').focus();
  }
}
</script>

<style>
  #app {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .main-text-container {
    width: 600px;
    height: 400px;
    font-size: 25px;
    color: transparent;
    font-family: "Times New Roman";
    background-color: transparent !important;
    caret-color: black;
  }

  .update-html {
    position: absolute;

    text-rendering: auto;
    color: initial;
    letter-spacing: normal;
    word-spacing: normal;
    text-transform: none;
    text-indent: 0;
    text-shadow: none;
    display: inline-block;
    text-align: start;
    margin: 0;
    font: 400 25px Times New Roman;

    -webkit-rtl-ordering: logical;
    flex-direction: column;
    cursor: text;
    white-space: pre-wrap;
    overflow-wrap: break-word;
    padding: 2px;
  }

  .modal-wrapper {
    position: absolute;
    left: 0;
    top: 0;
  }
</style>
