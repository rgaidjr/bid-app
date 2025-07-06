<template>
  <q-page padding>
    <div class="q-pa-md">
      <h4 class="q-mb-md">Emotional Needs Assessment</h4>
      <p class="q-mb-lg">Rate each emotional need on a scale of 1-5 for both importance to you and your satisfaction level.</p>

      <div class="q-gutter-y-md">
        <q-card v-for="(need, index) in emotionalNeeds" :key="index" class="q-mb-md">
          <q-card-section>
            <div class="text-h6">{{ need.title }}</div>
            <div class="text-subtitle2">{{ need.description }}</div>
          </q-card-section>

          <q-card-section>
            <div class="row q-col-gutter-md">
              <div class="col-12 col-md-6">
                <div class="text-subtitle2 q-mb-sm">Importance to you:</div>
                <q-rating
                  v-model="need.importance"
                  :max="5"
                  size="2em"
                  color="primary"
                  icon="star_border"
                  icon-selected="star"
                />
                <div class="text-caption q-mt-xs">
                  {{ getRatingDescription(need.importance) }}
                </div>
              </div>
              <div class="col-12 col-md-6">
                <div class="text-subtitle2 q-mb-sm">Your satisfaction level:</div>
                <q-rating
                  v-model="need.satisfaction"
                  :max="5"
                  size="2em"
                  color="secondary"
                  icon="star_border"
                  icon-selected="star"
                />
                <div class="text-caption q-mt-xs">
                  {{ getRatingDescription(need.satisfaction) }}
                </div>
              </div>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <div class="q-mt-lg flex justify-end">
        <q-btn color="primary" label="Summarize" @click="showSummary = true" />
        <!--q-btn color="secondary" label="Save Responses" @click="saveResponses" /-->
      </div>

      <q-dialog v-model="showSummary" full-width>
        <q-card class="full-width">
          <q-card-section>
            <div class="text-h6">Emotional Needs Summary</div>
          </q-card-section>

          <q-card-section>
            <div class="chart-container" style="height: 380px; position: relative;">
              <canvas ref="chartCanvas"></canvas>
            </div>
          </q-card-section>

          <!--q-card-section>
            <div class="text-subtitle2 q-mb-sm">Interpretation:</div>
            <ul>
              <li><strong>Top-Right:</strong> High importance, high satisfaction - Keep up the good work!</li>
              <li><strong>Top-Left:</strong> High importance, low satisfaction - Priority areas for improvement</li>
              <li><strong>Bottom-Right:</strong> Low importance, high satisfaction - Exceeding expectations</li>
              <li><strong>Bottom-Left:</strong> Low importance, low satisfaction - Lower priority areas</li>
            </ul>
          </q-card-section-->

          <q-card-actions align="right">
            <q-btn flat label="Close" color="primary" v-close-popup />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
  </q-page>
</template>

<script setup>
import { ref, watch } from 'vue';
import Chart from 'chart.js/auto';

const emotionalNeeds = ref([
  {
    title: 'Affection',
    description: 'Expressions of love through words, cards, flowers, gifts, hugs, kisses, etc.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Sexual Fulfillment',
    description: 'Sexual intimacy, frequency, and quality of sexual experiences',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Conversation',
    description: 'Talking about events of the day, feelings, ideas, etc.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Recreational Companionship',
    description: 'Sharing activities and interests together',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Honesty and Openness',
    description: 'Truthfulness, transparency, and sharing of feelings',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Physical Attractiveness',
    description: 'Taking care of personal appearance and hygiene',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Financial Support',
    description: 'Provision of financial resources for family needs',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Domestic Support',
    description: 'Help with household chores and responsibilities',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Family Commitment',
    description: 'Dedication to raising children and family activities',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Admiration',
    description: 'Being respected, appreciated, and valued',
    importance: 0,
    satisfaction: 0
  }
]);

const getRatingDescription = (rating) => {
  switch (rating) {
    case 1:
      return 'Very Low';
    case 2:
      return 'Low';
    case 3:
      return 'Moderate';
    case 4:
      return 'High';
    case 5:
      return 'Very High';
    default:
      return 'Not Rated';
  }
};

const showSummary = ref(false);
const chartCanvas = ref(null);
let chart = null;
/*
const saveResponses = () => {
  // In a real application, this would save to a database or local storage
  console.log('Saving responses:', emotionalNeeds.value);
  // You could add API calls here to save the data
};
*/
const createChart = () => {
  if (chart) {
    chart.destroy();
  }
  
  const ctx = chartCanvas.value.getContext('2d');
  
  // Filter out needs that haven't been rated
  const ratedNeeds = emotionalNeeds.value.filter(need => need.importance > 0 && need.satisfaction > 0);
  
  chart = new Chart(ctx, {
    type: 'scatter',
    data: {
      datasets: [{
        label: 'Emotional Needs',
        data: ratedNeeds.map(need => ({
          x: need.satisfaction,
          y: need.importance,
          label: need.title
        })),
        backgroundColor: 'rgba(75, 192, 192, 0.6)',
        borderColor: 'rgba(75, 192, 192, 1)',
        borderWidth: 1,
        pointRadius: 3,
        pointHoverRadius: 10
      }]
    },
    plugins: [{
      id: 'emotionalNeedsLabels',
      afterDatasetsDraw(chart) {
        const { ctx } = chart;
        const dataPoints = chart.data.datasets[0].data;
        const metaData = chart.getDatasetMeta(0).data;
        
        // Create a map to group points by their position
        const pointGroups = {};
        
        // Group points that are close to each other
        dataPoints.forEach((datapoint, index) => {
          const { x, y } = metaData[index].getCenterPoint();
          // Create a key based on rounded coordinates to group nearby points
          // Round to nearest 0.2 to consider points within this range as overlapping
          const key = `${Math.round(datapoint.x * 5) / 5},${Math.round(datapoint.y * 5) / 5}`;
          
          if (!pointGroups[key]) {
            pointGroups[key] = [];
          }
          pointGroups[key].push({ datapoint, x, y, index });
        });
        
        // Draw labels for each group, adjusting position to avoid overlap
        Object.values(pointGroups).forEach(group => {
          if (group.length === 1) {
            // Single point - draw normally
            const { datapoint, x, y } = group[0];
            ctx.save();
            ctx.font = '12px Arial';
            ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'bottom';
            ctx.fillText(datapoint.label, x, y - 10);
            ctx.restore();
          } else {
            // Multiple points - distribute labels
            const dist = 15;
            const baseX = group[0].x;
            const baseY = group[0].y - (dist/2)*(group.length-1);
            
            group.forEach((point, i) => {
              ctx.save();
              ctx.font = '12px Arial';
              ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
              ctx.textAlign = 'center';
              ctx.textBaseline = 'bottom';
              
              // Draw text with offset
              ctx.fillText(point.datapoint.label, baseX, baseY + (i*dist));
            
              ctx.restore();
            });
          }
        });
      }
    }],
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        x: {
          title: {
            display: true,
            text: 'Satisfaction',
            font: {
              size: 16,
              weight: 'bold'
            },
            padding: 10
          },
          min: 0,
          max: 6,
          ticks: {
            stepSize: 1
          }
        },
        y: {
          title: {
            display: true,
            text: 'Importance',
            font: {
              size: 16,
              weight: 'bold'
            },
            padding: 10
          },
          min: 0,
          max: 6,
          ticks: {
            stepSize: 1
          }
        }
      },
      plugins: {
        tooltip: {
          callbacks: {
            label: (context) => {
              const point = context.raw;
              return `${point.label}: Importance: ${point.y}, Satisfaction: ${point.x}`;
            }
          }
        },
        legend: {
          display: false
        }
      }
    }
  });
};

// Watch for dialog open to create/update chart
watch(showSummary, (newVal) => {
  if (newVal) {
    // Use nextTick to ensure the canvas is in the DOM
    setTimeout(() => {
      createChart();
    }, 100);
  }
});
</script>

<style scoped>
.q-rating >>> .q-icon {
  opacity: 0.8;
}
</style>
