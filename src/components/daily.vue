<template>
  <div class="heatmap">
    <div v-for="(row, rowIndex) in heatmapData" :key="rowIndex" class="heatmap-row">
      <div v-for="(value, colIndex) in row" :key="colIndex">
        <div v-if="rowIndex == 0" class="cell">
          <div v-if="colIndexToShowMonth.includes(colIndex)" class="heatmap-text" >
            {{ monthMap[value.month] }}
          </div>
        </div>
        <div class="flex-row">
          <div v-if="colIndex === 0" style="width: 40px;">
            <div v-if="rowIndex % 2 === 1" class="heatmap-text">
              {{ colIndexMapWeekDay[rowIndex] }}
            </div>
          </div>
          <el-popover trigger="click" placement="top" width="600" :disabled="!value.show">
            <template #reference>
              <div :style="getHeatmapCellStyle(value)" class="cell heatmap-cell cell-hover" :class="{ 'heatmap-cell-hover': value.show }">
              </div>
            </template>
            <div class="flex-horizontal-center" style="font-size: 18px;">
              {{ new Date(value.year, value.month - 1, value.day).toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric', year: 'numeric' }) }}
            </div>
            <div class="flex-row flex-horizontal-center">
              <div v-for="(moodRow, moodRowIndex) in colorMapMood" :key="moodRowIndex">
                <div style="padding-right: 10px;" class="flex-vertical-center mood-cell cell-hover"
                  @click="selectMood(rowIndex, colIndex, moodRowIndex)">
                  <div :style="`background-color: ${colorMap[moodRowIndex]}`" class="cell"
                    style="margin-right: 5px;"></div>
                  {{ moodRow }}
                </div>
              </div>
            </div>
          </el-popover>
        </div>
      </div>
    </div>
  </div>
</template>
  
  
<script>
import { ElPopover } from 'element-plus';

export default {
  components: {
    ElPopover,
  },
  props: {
    heatmapData: {
      type: Array,
      required: true
    },
    colIndexToShowMonth: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      colorMap: {
        0: '#f0f0f0', // gray: low energy
        1: '#d2fcdf', // green: energetic
        2: '#d4bcbc', // red: normal
        3: '#ffbae5', // pink: love
        4: '#d4ddfa', // blue: sad
        5: '#fcfbc2', // yellow: happy
      },
      colorMapMood: {
        0: 'low energy',
        1: 'energetic',
        2: 'normal',
        3: 'love',
        4: 'sad',
        5: 'happy',
      },
      colIndexMapWeekDay: {
        0: 'Sun',
        1: 'Mon',
        2: 'Tue',
        3: 'Wed',
        4: 'Thu',
        5: 'Fri',
        6: 'Sat',
      },
      monthMap: {
        1: 'Jan',
        2: 'Feb',
        3: 'Mar',
        4: 'Apr',
        5: 'May',
        6: 'Jun',
        7: 'Jul',
        8: 'Aug',
        9: 'Sep',
        10: 'Oct',
        11: 'Nov',
        12: 'Dec',
      },
      cellRef: null,
    };
  },
  methods: {
    getHeatmapCellStyle(value) {
      if (value.show) {
        return `background-color: ${this.colorMap[value.mood]}`;
      }
      return `background-color: white`;
    },
    selectMood(rowIndex, colIndex, moodIndex) {
      this.heatmapData[rowIndex][colIndex].mood = moodIndex;
    },

  }
}
</script>
  
  
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins&display=swap');

div {
  font-family: Poppins, Arial, sans-serif;
}

.cell {
  width: 20px;
  height: 20px;
}

.heatmap-text {
  font-size: 13px;
  color: #a1a1a1;
}

.heatmap {
  display: flex;
  flex-direction: column;
}

.heatmap-row {
  display: flex;
}

.heatmap-cell {
  border: 2px solid white;
}

.flex-horizontal-center {
  display: flex;
  justify-content: center;
}

.flex-vertical-center {
  display: flex;
  align-items: center;
}

.flex-row {
  display: flex;
  flex-direction: row;
}

.mood-cell {
  border: 2px solid white;
  padding: 5px;
}

.cell-hover:hover {
  cursor: pointer;
  border: 2px solid blue;
}

</style>