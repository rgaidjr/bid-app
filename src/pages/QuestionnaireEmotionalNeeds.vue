<template>
  <q-page padding>
    <div class="q-pa-md">
      <h3 class="page-title q-mb-md">Emotional Needs Assessment</h3>
      <p class="intro-text q-mb-lg">
        Rate each emotional need on a scale of 1-5 (with 1 star as the lowest and 5 stars as the highest).
        <br/>
        Rate (i) how important this need is for you, and (ii) how satisfied are you with your spouse's ability to fill this need.
      </p>

      <div class="q-gutter-y-md">
        <q-card v-for="(need, index) in emotionalNeeds" :key="index" class="q-mb-md">
          <q-card-section>
            <div class="text-h6">{{ need.title }}</div>
            <div class="text-subtitle2">{{ need.description }}</div>
          </q-card-section>

          <q-card-section>
            <div class="row q-col-gutter-md">
              <div class="col-12 col-md-6">
                <div class="text-subtitle2 q-mb-sm">Importance of this need for you:</div>
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
                <div class="text-subtitle2 q-mb-sm">Your satisfaction level of your spouse's ability to fill this need:</div>
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
    description: 'Nonsexual expression of care through words, cards, gifts, hugs, kisses, and courtesies; creating an environment that clearly and repeatedly expresses care.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Sexual Fulfillment',
    description: 'A sexual experience that is predictably enjoyable and frequent enough for you.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Intimate Conversation',
    description: 'Talking about feelings, topics of personal interest/ opinions, and plans.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Recreational Companionship',
    description: 'Leisure activities with at least one other person.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Honesty and Openness',
    description: 'Truthful and frank expression of positive and negative feelings, events of the past, daily events and schedule, and plans for the future; not leaving a false impression.',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Physical Attractiveness',
    description: 'Viewing physical traits of the opposite sex that are aesthetically and/or sexually pleasing',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Financial Support',
    description: 'Provision of the financial resources to house, feed, and clothe your family at a standard of living acceptable to you',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Domestic Support',
    description: 'Management of the household tasks and care of the children—if any are at home—that create a home environment that offers you a refuge from stress',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Family Commitment',
    description: 'Provision for the moral and educational development of your children within the family unit',
    importance: 0,
    satisfaction: 0
  },
  {
    title: 'Admiration',
    description: 'Being shown respect, value, and appreciation',
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
        backgroundColor: 'rgba(126, 87, 194, 0.6)', // Purple from primary color
        borderColor: 'rgba(126, 87, 194, 1)',
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

.q-page {
  background-color: #f8f5fd;
}

.page-title {
  color: #7E57C2;
  font-weight: 700;
  font-size: 2rem;
  letter-spacing: 0.5px;
  margin-bottom: 1rem;
  border-bottom: 2px solid #EC407A;
  padding-bottom: 0.5rem;
  display: inline-block;
}

.intro-text {
  color: #616161;
  font-size: 1.1rem;
  line-height: 1.6;
  max-width: 800px;
  margin-bottom: 2rem;
}

.text-h6 {
  color: #5E35B1;
  font-weight: 600;
  letter-spacing: 0.5px;
}

.text-subtitle2 {
  color: #616161;
  font-weight: 400;
  line-height: 1.5;
  margin-top: 6px;
}

.q-card {
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  transition: transform 0.2s, box-shadow 0.2s;
}

.q-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.q-card-section {
  padding: 20px;
}

.text-caption {
  color: #7E57C2;
  font-weight: 500;
}

/* Style for the dialog */
.chart-container {
  background-color: white;
  border-radius: 8px;
  padding: 10px;
}
</style>
