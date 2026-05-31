<template>
  <div class="shared-page-background">
    <div class="content-page">
      <div class="centered-content">
        <div class="story-box">
          <template v-if="!finished">
            <p class="quiz-meta">Vraag {{ index + 1 }} van {{ questions.length }} · Goed: {{ score }}</p>
            <h2 class="story-box-name">{{ question.question }}</h2>

            <div v-if="!answered" class="quiz-options">
              <button
                v-for="letter in ['A', 'B', 'C']"
                :key="letter"
                type="button"
                class="quiz-option"
                @click="answer(letter)"
              >
                <span class="quiz-option-letter">{{ letter }}</span>
                {{ question.options[letter] }}
              </button>
            </div>

            <div v-else class="quiz-feedback" :class="correct ? 'quiz-feedback--ok' : 'quiz-feedback--no'">
              <p v-if="correct">Goed zo! Dat klopt.</p>
              <p v-else>
                Nog niet goed. Het juiste antwoord is
                <strong>{{ question.correct }}</strong>:
                {{ question.options[question.correct] }}
              </p>
              <button type="button" class="quiz-btn" @click="next">
                {{ index === questions.length - 1 ? 'Bekijk score' : 'Volgende vraag' }}
              </button>
            </div>
          </template>

          <template v-else>
            <h2 class="story-box-name">Klaar!</h2>
            <p class="story-box-story">
              Je hebt <strong>{{ score }}</strong> van de <strong>{{ questions.length }}</strong> vragen goed.
            </p>
            <button type="button" class="quiz-btn" @click="restart">Opnieuw spelen</button>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import questions from '../assets/quiz.json';
import '../styles/shared-page.css';
import './GamePage.css';
import './StoryBox.css';

type Letter = 'A' | 'B' | 'C';

const index = ref(0);
const score = ref(0);
const answered = ref(false);
const correct = ref(false);
const finished = ref(false);

const question = computed(() => questions[index.value] as {
  question: string;
  options: Record<Letter, string>;
  correct: Letter;
});

function answer(letter: string) {
  if (answered.value) return;
  answered.value = true;
  correct.value = letter === question.value.correct;
  if (correct.value) score.value++;
}

function next() {
  if (index.value === questions.length - 1) {
    finished.value = true;
    return;
  }
  index.value++;
  answered.value = false;
}

function restart() {
  index.value = 0;
  score.value = 0;
  answered.value = false;
  correct.value = false;
  finished.value = false;
}
</script>
