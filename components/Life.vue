<template>
  <div class="life">
    <div id="area" class="life-area">
      <template v-for="(row, rowIndex) in currentGeneration">
        <template v-for="(col, colIndex) in row">
          <div
               :key="`r${rowIndex}c${colIndex}`"
               class="life-area__cell"
               :class="col ? 'alive': 'dead'"
               @click="changeCell(rowIndex, colIndex)"/>
        </template>
      </template>
    </div>
    <div class="life-controls">
      <button class="life-button" :disabled="intervalId" @click="startCycle">Старт</button>
      <button class="life-button" :disabled="!intervalId" @click="stopCycle">Стоп</button>
      <button class="life-button" @click="resetCurrentGeneration">Сбросить</button>
      <button class="life-button" @click="putPlaner">Планер</button>
    </div>
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
      arrayDimension: 50,
      currentGeneration: []
    }
  },
  created() {
    this.resetCurrentGeneration();
  },
  mounted() {
    const area = document.getElementById('area');
    const dimension = 1000;
    area.style.height = `${dimension}px`;
    area.style.width = `${dimension}px`;
    area.style.gridTemplateColumns = `repeat(${this.arrayDimension}, ${dimension/this.arrayDimension}px)`;
    area.style.gridTemplateRows = `repeat(${this.arrayDimension}, ${dimension/this.arrayDimension}px)`;
  },
  methods: {
    resetCurrentGeneration() {
      if(this.intervalId) {
        clearInterval(this.intervalId);
        this.intervalId = null;
      }
      this.currentGeneration = [];
      this.currentStatus = '';
      this.currentStep = 0;
      for(let row = 0; row < this.arrayDimension; row ++) {
        const rows = [];
        for(let col = 0; col < this.arrayDimension; col ++) {
          rows.push(0);
        }
        this.currentGeneration.push(rows);
      }
    },
    putPlaner() {
      this.currentGeneration[0][2] = 1;
      this.currentGeneration[1][2] = 1;
      this.currentGeneration[2][2] = 1;
      this.currentGeneration[2][1] = 1;
      this.currentGeneration[1][0] = 1;
      this.currentGeneration = [...this.currentGeneration];
    },
    changeCell(row, col) {
      this.currentGeneration[row][col] = this.currentGeneration[row][col] ? 0 : 1;
      this.currentGeneration = [...this.currentGeneration];
    },
    startCycle() {
      if(!this.intervalId)
        this.intervalId = setInterval(this.cycleGeneration, 250);
    },
    stopCycle() {
      if(this.intervalId) {
        clearInterval(this.intervalId);
        this.intervalId = null;
      }
    },
    checkAll() {
      let counter = 0;
      for(let row = 0; row < this.arrayDimension; row++){
        for(let col = 0; col < this.arrayDimension; col++) {
          counter += this.currentGeneration[row][col];
        }
      }
      return !!counter;
    },
    cycleGeneration() {
      this.currentStep++;
      this.currentStatus = `Шаг ${this.currentStep}`;
      if(!this.checkAll()) {
        this.currentStatus = `Stopped on ${this.currentStep} step`;
        clearInterval(this.intervalId);
        this.intervalId = null;
      }
      const nextGeneration = [];
      for(let row = 0; row < this.arrayDimension; row++) {
        const rows = [];
        for(let col = 0; col < this.arrayDimension; col++)
        {
          rows.push(this.checkCell(row, col));
        }
        nextGeneration.push(rows);
      }
      this.currentGeneration = [...nextGeneration];
    },
    checkCell(row, col) {
      const neighbours = this.countNeighbours(row, col);
      if(this.currentGeneration[row][col])
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
    countNeighbours(row, col) {
      const rowsToCount = this.itemsToCount(row);
      const colsToCount = this.itemsToCount(col);
      let counter = 0;
      rowsToCount.forEach(rowCount => {
        colsToCount.forEach(colCount => {
          if(!(rowCount === row && colCount === col))
            counter += this.currentGeneration[rowCount][colCount];
        })
      })
      return counter;
    },

    itemsToCount(item) {
      let itemsToCount = [];
      if(item > 0 && item < this.arrayDimension - 1)
        itemsToCount = [item - 1, item, item + 1];
      if(item === 0)
        itemsToCount = [this.arrayDimension - 1, item, item + 1];
      if(item === this.arrayDimension - 1)
        itemsToCount = [item - 1, item, 0];
      return itemsToCount;
    }
  }
}
</script>

