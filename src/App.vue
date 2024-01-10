<template>
  <div style="display: flex; justify-content: center; align-items: center; height: 90vh; flex-direction: column;">
    <h1>
      <span style="color: #d2fcdf;">Your </span> 
      <span style="color: #ffbae5;">Daily </span>
      <span style="color: #d4ddfa">Mood </span> 
      <!-- <span style="color: #000000">2024 </span> -->
    </h1>
    <daily :heatmapData="heatmapData" :colIndexToShowMonth="colIndexToShowMonth"></daily>
  </div>
</template>

<script>
import daily from './components/daily.vue';

export default {
  components: {
    daily,
  },
  data() {
    return {
      heatmapData: [],
      colIndexToShowMonth: [],
    };
  },
  mounted() {
    this.loadHeatmapData();
    // this.generateHeatmapData(2024);
    console.log(this.heatmapData);
    console.log(this.colIndexToShowMonth);

    window.addEventListener('beforeunload', this.saveHeatmapData);
  },
  beforeUnmount() {
    // Unregister the beforeunload event listener
    window.removeEventListener('beforeunload', this.saveHeatmapData);
  },
  methods: {
    generateHeatmapData(year) {
      let currentDay = 1;
      let currentMonth = 1;
      let currentYear = year;
      let dayInFebruary = 28;
      let ShowedMonth = 0;
      if ((year % 4 === 0 && year % 100 !== 0) || year % 400 === 0) {
        dayInFebruary = 29; // Leap year
      }
      let numberOfDaysInEachMonth = [31, dayInFebruary, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];

      let firstDay = new Date(year, 0, 1);
      let firstDayInWeek = firstDay.getDay();

      if (firstDayInWeek > 0) {
        currentYear = year - 1;
        currentMonth = 12;
        currentDay = 31 - firstDayInWeek + 1;
      }

      for (let i = 0; i < 53; i++) {
        const row = [];
        console.log(currentMonth + ' ' + ShowedMonth);
        if (currentMonth != ShowedMonth) {
          ShowedMonth = currentMonth;
          if (currentMonth > 0 && currentMonth < 13 && currentYear == year) {
            this.colIndexToShowMonth.push(i);
          }
        }
        for (let j = 0; j < 7; j++) {
            // random mood from 0 to 5
            let mood = Math.floor(Math.random() * 6);

          let temp = { mood: 0, year: currentYear, month: currentMonth, day: currentDay, show: true };

          if (currentYear != year) {
            temp.show = false;
          }

          row.push(temp);

          currentDay++;
          if (currentDay > numberOfDaysInEachMonth[currentMonth - 1]) {
            currentDay = 1;
            currentMonth++;
          }
          if (currentMonth === 13) {
            currentMonth = 1;
            currentYear++;
          }
        }
        this.heatmapData.push(row);

      }
      function transposeArray(array) {
        const rows = array.length;
        const cols = array[0].length;

        const transposedArray = [];
        for (let col = 0; col < cols; col++) {
          const newRow = [];
          for (let row = 0; row < rows; row++) {
            newRow.push(array[row][col]);
          }
          transposedArray.push(newRow);
        }

        return transposedArray;
      }

      this.heatmapData = transposeArray(this.heatmapData);

      this.saveHeatmapData();
    },
    saveHeatmapData() {
      // Save the data to local storage
      localStorage.setItem('heatmapData', JSON.stringify(this.heatmapData));
      localStorage.setItem('colIndexToShowMonth', JSON.stringify(this.colIndexToShowMonth));
    },
    loadHeatmapData() {
      const storedHeatmapData = localStorage.getItem('heatmapData');
      const storedColIndexToShowMonth = localStorage.getItem('colIndexToShowMonth');

      if (storedHeatmapData && storedColIndexToShowMonth) {
        this.heatmapData = JSON.parse(storedHeatmapData);
        console.log("Stored heatmapData");
        console.log(this.heatmapData);
        this.colIndexToShowMonth = JSON.parse(storedColIndexToShowMonth);
      } else {
        // Generate the data if it doesn't exist in local storage
        this.generateHeatmapData(2024);
      }
    },
  }
}

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins&display=swap');

div {
  font-family: Poppins, Arial, sans-serif;
}
</style>
