<template>
  <h4>Carts
    <span v-if="loading">Loading...</span>
  </h4>
  <div class="select-section">
    <div class="select-column">
      <input
          class="input-search"
          type="text"
          placeholder="Search for cart"
          v-model="input1"
      >
      <div class="list-wrapper">
        <div
            v-for="{id, text, selected} in filteredList1"
            :key="id"
            :class="{'selected' : selected}"
            @click="handleSelect(id, 'list1')"
        >
          {{ text }}
        </div>
      </div>
    </div>

    <div class="controls">
      <button
          type="button"
          @click="mergeList('list1', 'list2')"
      >
        <img src="@/assets/img/svg/angle-double-right.svg" alt="">
      </button>
      <button
          type="button"
          @click="moveSelectedProducts('list1', 'list2')"
      >
        <img src="@/assets/img/svg/angle-right.svg" alt="">
      </button>
      <button
          type="button"
          class="rotate-180"
          @click="moveSelectedProducts('list2', 'list1')"
      >
        <img src="@/assets/img/svg/angle-right.svg" alt="">
      </button>
      <button
          type="button"
          class="rotate-180"
          @click="mergeList('list2', 'list1')"
      >
        <img src="@/assets/img/svg/angle-double-right.svg" alt="">
      </button>
    </div>

    <div class="select-column">
      <input
          class="input-search"
          type="text"
          placeholder="Search for cart"
          v-model="input2"
      >

      <div class="list-wrapper">
        <div
            v-for="{id, text, selected} in filteredList2"
            :key="id"
            :class="{'selected' : selected}"
            @click="handleSelect(id, 'list2')"
        >
          {{ text }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      list1: [],
      list2: [],
      input1: '',
      input2: '',
      loading: false,
    }
  },
  created() {
    this.callApi();
  },
  methods: {
    callApi() {
      this.loading = true;
      fetch('https://api.publicapis.org/entries')
          .then((res) => res.json())
          .then((res) => {
                this.list1 = this.remapApiData(res.entries);
                this.loading = false;
              }
          );
    },

    remapApiData(data) {
      return Object.values(data).map((item, index) => {
        return {id: index, text: item.API}
      })
    },

    mergeList(sourceListName, targetListName) {
      if (this[sourceListName].length === 0) return;

      this[targetListName] = [...this[sourceListName], ...this[targetListName]];
      this[sourceListName] = [];
      this[targetListName].sort((a, b) => a.id - b.id);
    },

    moveSelectedProducts(sourceListName, targetListName) {
      const movableItems = [];

      this[sourceListName] = this[sourceListName].filter((item => {
        if (item.selected !== true) return true;
        movableItems.push(item);
        return false;
      }))

      this[targetListName] = [...this[targetListName], ...movableItems];
      this[targetListName].sort((a, b) => a.id - b.id);
    },

    handleSelect(id, listName) {
      const selectedObj = this[listName].find(item => item.id === id);
      selectedObj.selected = !selectedObj.selected;
    },
  },

  computed: {
    filteredList1() {
      return this.list1.filter((item) => item.text.toLowerCase().includes(this.input1.toLowerCase()))
    },

    filteredList2() {
      return this.list2.filter((item) => item.text.toLowerCase().includes(this.input2))
    },
  },
}
</script>

<style lang="scss">
@import "assets/scss/main";
</style>
