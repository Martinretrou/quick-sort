<template>
  <section>
    <!-- <button @click="quickSort(items, 0, items.length - 1)">Sort</button> -->
    <div class="sort-container">
      <transition-group name="list-complete" tag="div" class="bar-container">
        <div class="bar" :class="[{ 'active' : states[i] === 0 }, { 'parsing' : states[i] === 1 }]" :style="{ height: item + '%', width : 100 / items.length + '%' }" v-for="(item, i) in items" :key="`${item}${i}`" />
      </transition-group>
    </div>
  </section>
</template>

<script>
export default {
  name:"SortContainer",
  data() {
    return {
      nbOfItems: 100,
      items: [],
      pivotIndex: 0,
      pivotValue: 0,
      states: []
    }
  },
  async created() {
    await this.generateArray()
  },
  async mounted() {
    if (this.items.length === this.nbOfItems) await this.quickSort(this.items, 0, this.items.length - 1)
  },
  methods: {
    async generateArray() {
      for (let index = 0; index < this.nbOfItems; index++) {
        this.items.push(Math.random() * 100)
        this.states[index] = -1
      }
    },
    async quickSort(arr, start, end) {
      if (start >= end) return
      let index = await this.partition(arr, start, end)
      this.states[index] = -1
      console.log('Sorting...')
      await Promise.all([this.quickSort(arr, start, index - 1), this.quickSort(arr, index + 1, end)])
      this.$forceUpdate()
    },
    async partition(arr, start, end) {
      for (let i = start; i < end; i++) {
        if(i != pivotIndex) this.states[i] = 1
      }
      let pivotValue = arr[end]
      let pivotIndex = start
      this.states[pivotIndex] = 0
      for (let i = start; i < end; i++) {
        if(arr[i] < pivotValue) {
          await this.swap(arr, i, pivotIndex)
          this.states[pivotIndex] = -1
          pivotIndex++
          this.states[pivotIndex] = 0
        }
      }
      await this.swap(arr, pivotIndex, end)
      for (let i = start; i < end; i++) {
        if(i != pivotIndex) this.states[i] = -1
      }
      this.$forceUpdate()
      return pivotIndex
    },
    async swap(arr, a, b) {
      await this.sleep(25)
      let temp = arr[a]
      arr[a] = arr[b]
      arr[b] = temp
    },
    async sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
  }
}
</script>


<style>

.sort-container {
  width: 100vw;
  height: 100vh;
  background-color: #f6f5f5;
  display: flex;
  align-items: flex-end;
  justify-content: space-evenly;
}

.bar-container {
  height: 100%;
  width: 100%;
  display: flex;
  align-items: flex-end;
  justify-content: center;
}

.bar {
  flex: 1;
  margin-left: -2px;
  border-radius: 3px 3px 0 0;
  /* border: 1px solid black; */
  display: inline-block;
  background-color: #87c0cd;
  opacity: .8;
  transition: all 50ms;
}

.active {
  background-color: #113f67;
}

.parsing {
  background-color: #226597;
}

.list-complete-enter, .list-complete-leave-to
/* .list-complete-leave-active below version 2.1.8 */ {
  opacity: 0;
  /* transform: translateY(30px); */
}
.list-complete-leave-active {
  position: absolute;
}

</style>
