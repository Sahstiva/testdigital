<template>
  <div class="life">
    <div class="life-area">
      <template v-for="(row, rowIndex) in currentGeneration">
        <template v-for="(col, colIndex) in row">
          <div :key="`${rowIndex}${colIndex}`" class="life-area__cell" :class="col ? 'alive': 'dead'"/>
        </template>
      </template>
    </div>
    <button class="life-button" @click="startCycle">Старт</button>
    <button class="life-button" @click="stopCycle">Стоп</button>
    <div class="life-text">{{ currentStatus }}</div>
  </div>
</template>

<script>
import '~/assets/style.scss';

export default {
  name: "LifeCycle",
  data() {
    return {
      currentStep: 0,
      intervalId: null,
      currentStatus: '',
      currentGeneration: [
        [0, 0, 0, 0, 0],
        [0, 0, 0, 1, 0],
        [0, 1, 0, 1, 0],
        [0, 0, 1, 1, 0],
        [0, 0, 0, 0, 0]
      ]
    }
  },
  methods: {
    startCycle() {
      this.intervalId = setInterval(this.cycleGeneration, 1000);
    },
    stopCycle() {
      clearInterval(this.intervalId);
    },
    checkAll() {
      let counter = 0;
      for(let row = 0; row < 5; row++){
        for(let col = 0; col < 5; col++) {
          counter += this.currentGeneration[row][col];
        }
      }
      return !!counter;
    },
    cycleGeneration() {
      this.currentStep++;
      this.currentStatus = `Шаг ${this.currentStep}`;
      if(!this.checkAll()) {
        // eslint-disable-next-line no-console
        this.currentStatus = `Stopped on ${this.currentStep} step`;
        clearInterval(this.intervalId);
      }
      const nextGeneration = [
        [0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0]
      ];
      for(let row = 0; row < 5; row++) {
        // console.log(`row ${row}`);
        for(let col = 0; col < 5; col++)
        {
          // console.log(`column ${col}`);
          nextGeneration[row][col] = this.checkCell(this.currentGeneration, row, col);
        }
      }
      this.currentGeneration = nextGeneration;
      // console.log(nextGeneration);
      // return nextGeneration;
    },
    checkCell(current, row, col) {
      const neighbours = this.countNeighbours(current, row, col);
      if(current[row][col])
        if(neighbours < 4 && neighbours > 1)
          return 1;
        else
          return 0;
      else
      if(neighbours === 3)
        return 1;
      else
        return 0;
    },
    countNeighbours(current, row, col) {
      const rowsToCount = this.itemsToCount(row);
      const colsToCount = this.itemsToCount(col);
      let counter = 0;
      rowsToCount.forEach(rowCount => {
        colsToCount.forEach(colCount => {
          if(!(rowCount === row && colCount === col))
            counter += current[rowCount][colCount];
        })
      })
      return counter;
    },

    itemsToCount(item) {
      let itemsToCount = [];
      if(item > 0 && item < 4)
        itemsToCount = [item - 1, item, item +1];
      if(item === 0)
        itemsToCount = [4, item, item + 1];
      if(item === 4)
        itemsToCount = [item - 1, item, 0];
      return itemsToCount;
    }
  }
}
</script>

