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
        <q-btn color="primary" label="Save Responses" @click="saveResponses" />
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref } from 'vue';

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

const saveResponses = () => {
  // In a real application, this would save to a database or local storage
  console.log('Saving responses:', emotionalNeeds.value);
  // You could add API calls here to save the data
};
</script>

<style scoped>
.q-rating >>> .q-icon {
  opacity: 0.8;
}
</style>
