<script setup>
import {ref} from "vue";
import QuizStart from "@/components/QuizStart.vue";
import UserForm from "@/components/UserForm.vue";

const currentQuestion = ref(-1);
const selectedAnswers = ref({});
const isSubmitting = ref(false);
const shakeOptions = ref(false);
const questions = ref([
  {
    text: "Где будет устанавливаться натяжной потолок?",
    isMulti: true,
    options: [
      {
        variant: "Вся квартира (дом)"
      },
      {
        variant: "Комната"
      },
      {
        variant: "Коридор"
      },
      {
        variant: "Кухня"
      },
      {
        variant: "Ванная / туалет"
      },
      {
        variant: "Другое"
      }
    ]
  },
  {
    text: "Выберите конструкцию потолка",
    isMulti: true,
    options: [
      {
        variant: "Одноуровневый"
      },
      {
        variant: "Многоуровневый"
      },
      {
        variant: "Волнообразный"
      },
      {
        variant: " Имитация звездного неба"
      }
    ]
  },
  {
    text: "Выберите вид фактуры",
    isMulti: true,
    options: [
      {
        variant: "Матовый"
      },
      {
        variant: "Глянцевый"
      },
      {
        variant: "Сатиновый"
      },
      {
        variant: "Фотопечать"
      }
    ]
  },
  {
    text: "Требуется звукоизоляция?",
    isMulti: false,
    options: [
      {
        variant: "Да"
      },
      {
        variant: "Нет"
      }
    ]
  },
  {
    text: "Выберите ваш подарок к потолку",
    isMulti: false,
    options: [
      {
        variant: "Декоративная вставка (белая)"
      },
      {
        variant: "Скидка 7% за прохождение теста"
      },
      {
        variant: "Потолочный карниз"
      },
      {
        variant: "Установка люстры"
      },
      {
        variant: "Потолочные светильники"
      }
    ]
  }
]);

const userData = ref({
  name: '',
  phone: ''
});

const selectSingleOption = (questionIndex, optionIndex) => {
  selectedAnswers.value[questionIndex] = optionIndex;
};

const toggleMultiOption = (questionIndex, optionIndex) => {
  // Инициализируем массив, если его нет
  if (!Array.isArray(selectedAnswers.value[questionIndex])) {
    selectedAnswers.value[questionIndex] = [];
  }

  const index = selectedAnswers.value[questionIndex].indexOf(optionIndex);
  if (index > -1) {
    selectedAnswers.value[questionIndex].splice(index, 1);
  } else {
    selectedAnswers.value[questionIndex].push(optionIndex);
  }
};

const isOptionSelected = (questionIndex, optionIndex) => {
  if (!Array.isArray(selectedAnswers.value[questionIndex])) {
    return false;
  }
  return selectedAnswers.value[questionIndex].includes(optionIndex);
};

const nextQuestion = () => {
  // Проверяем, что ответ выбран
  if (currentQuestion.value >= 0 && currentQuestion.value < questions.value.length) {
    const answer = selectedAnswers.value[currentQuestion.value];
    const hasAnswer = answer !== undefined && answer !== null;
    const isEmptyArray = Array.isArray(answer) && answer.length === 0;

    // Если ответа нет вообще или это пустой массив (для множественного выбора)
    if (!hasAnswer || isEmptyArray) {
      // Активируем анимацию shake
      shakeOptions.value = true;
      setTimeout(() => {
        shakeOptions.value = false;
      }, 1000);
      return;
    }
  }

  if (currentQuestion.value < questions.value.length + 1) {
    currentQuestion.value++;
  }
};

const prevQuestion = () => {
  if (currentQuestion.value > -1) {
    currentQuestion.value--;
  }
};

const restartQuiz = () => {
  currentQuestion.value = 0;
  selectedAnswers.value = {};
  userData.value = {
    name: '',
    phone: ''
  };
};

const submitQuiz = async () => {
  if (isSubmitting.value) return;

  isSubmitting.value = true;

  // Формируем данные для отправки
  const formattedAnswers = questions.value.map((question, index) => {
    const answer = selectedAnswers.value[index];
    let selectedOptions = [];

    if (question.isMulti && Array.isArray(answer)) {
      // Для множественного выбора
      selectedOptions = answer.map(optionIndex => question.options[optionIndex].variant);
    } else if (answer !== undefined) {
      // Для единичного выбора
      selectedOptions = [question.options[answer].variant];
    }

    return {
      question: question.text,
      answers: selectedOptions
    };
  });

  const quizData = {
    userData: userData.value,
    answers: formattedAnswers,
    timestamp: new Date().toISOString()
  };

  console.log('Отправка данных квиза:', quizData);

  try {
    // Здесь будет отправка на сервер
    const response = await fetch('/api/submit-quiz', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(quizData)
    });

    if (response.ok) {
      const result = await response.json();
      console.log('Ответ сервера:', result);
      alert('Спасибо! Ваша заявка успешно отправлена. Мы свяжемся с вами в ближайшее время.');

      // Можно добавить переход на страницу благодарности или сброс квиза
      restartQuiz();
    } else {
      throw new Error('Ошибка при отправке данных');
    }
  } catch (error) {
    console.error('Ошибка отправки:', error);
    // Временно просто показываем данные в консоли
    alert('Данные подготовлены к отправке. Проверьте консоль для просмотра данных.');
  } finally {
    isSubmitting.value = false;
  }
};
</script>

<template>
  <div class="quiz-wrapper">
    <div class="quiz-container">
      <!-- Progress Bar -->
      <div class="progress-bar-container" v-show="currentQuestion >= 0">
        <div class="progress-info">
          <span class="progress-text">Шаг {{ currentQuestion + 1 }} из {{ questions.length + 1 }}</span>
          <span class="progress-percentage">{{ Math.round((currentQuestion / questions.length) * 100) }}%</span>
        </div>
        <div class="progress-bar">
          <div class="progress-bar-fill" :style="{ width: `${(currentQuestion / questions.length) * 100}%` }"></div>
        </div>
      </div>

      <div class="question-container">
        <!-- Start Screen -->
        <QuizStart v-show="currentQuestion === -1"/>

        <!-- Questions -->
        <div class="question" v-for="(question, ind) in questions" :key="`question-${ind}`">
          <div v-show="ind === currentQuestion" class="question-content">
            <div class="question-header">
              <div class="question-number">Вопрос {{ ind + 1 }}</div>
              <h2 class="question-title">{{ question.text }}</h2>
            </div>

            <div class="options-grid" :class="{ 'animate__shakeX': shakeOptions && ind === currentQuestion }">
              <!-- Single Choice Options (Radio) -->
              <div class="option-card"
                   v-show="!question.isMulti"
                   v-for="(option, index) in questions[ind].options"
                   :key="`single-${index}`"
                   :class="{ 'option-selected': selectedAnswers[ind] === index }"
                   @click="selectSingleOption(ind, index)">
                <div class="option-radio">
                  <div class="radio-outer">
                    <div class="radio-inner" v-show="selectedAnswers[ind] === index"></div>
                  </div>
                </div>
                <span class="option-label">{{ option.variant }}</span>

              </div>

              <!-- Multiple Choice Options (Checkbox) -->
              <div class="option-card"
                   v-show="question.isMulti"
                   v-for="(option, index) in questions[ind].options"
                   :key="`multi-${index}`"
                   :class="{ 'option-selected': isOptionSelected(ind, index) }"
                   @click="toggleMultiOption(ind, index)">
                <div class="option-checkbox">
                  <div class="checkbox-outer">
                    <div class="checkbox-inner" v-show="isOptionSelected(ind, index)">
                      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
                        <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
                      </svg>
                    </div>
                  </div>
                </div>
                <span class="option-label">{{ option.variant }}</span>
              </div>
            </div>
          </div>
        </div>

        <!-- User Form -->
        <UserForm
            v-show="currentQuestion === questions.length"
            :userData="userData"
            :isSubmitting="isSubmitting"
            @submit="submitQuiz"
        />
      </div>

      <!-- Footer with Navigation Buttons -->
      <div class="quiz-footer">
        <div v-show="currentQuestion === -1" class="start-button-container">
          <button @click="nextQuestion" class="btn-start">
            <span class="btn-icon">🎁</span>
            <div class="btn-content">
              <span class="btn-main-text">Узнать стоимость потолка сейчас</span>
              <span class="btn-sub-text">+ получить гарантированный подарок за прохождение теста</span>
            </div>
          </button>
        </div>

        <div v-show="currentQuestion >= 0 && currentQuestion < questions.length" class="navigation-buttons">
          <button @click="prevQuestion" class="btn-nav btn-back" v-show="currentQuestion > 0">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
              <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z"/>
            </svg>
            Назад
          </button>
          <div v-show="currentQuestion === 0"></div>
          <button @click="nextQuestion" class="btn-nav btn-next">
            Далее
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
              <path d="M8.59 16.59L10 18l6-6-6-6-1.41 1.41L13.17 12z"/>
            </svg>
          </button>
        </div>

      </div>
    </div>
  </div>
</template>

<style scoped>
.quiz-wrapper {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem 1rem;
}

.quiz-container {
  max-width: 800px;
  width: 100%;
  background: rgba(250, 250, 250, 0.98);
  backdrop-filter: blur(10px);
  border-radius: 24px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
  overflow: hidden;
  animation: slideUp 0.6s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Progress Bar */
.progress-bar-container {
  padding: 2rem 2rem 1rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.progress-info {
  display: flex;
  justify-content: space-between;
  margin-bottom: 0.75rem;
  font-weight: 600;
  font-size: 0.95rem;
}

.progress-bar {
  height: 8px;
  background: rgba(255, 255, 255, 0.3);
  border-radius: 10px;
  overflow: hidden;
}

.progress-bar-fill {
  height: 100%;
  background: linear-gradient(90deg, #ffd89b 0%, #19547b 100%);
  border-radius: 10px;
  transition: width 0.4s ease;
  box-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
}

/* Question Container */
.question-container {
  padding: 2.5rem;
  min-height: 400px;
}

.question-content {
  animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.question-header {
  margin-bottom: 2rem;
}

.question-number {
  display: inline-block;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 0.4rem 1rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  margin-bottom: 1rem;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
}

.question-title {
  font-size: 1.75rem;
  font-weight: 700;
  color: #2d3748;
  line-height: 1.4;
  margin: 0;
}

.question-hint {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  margin-top: 0.75rem;
  padding: 0.5rem 1rem;
  background: linear-gradient(135deg, rgba(16, 185, 129, 0.1) 0%, rgba(5, 150, 105, 0.1) 100%);
  border: 1.5px solid rgba(16, 185, 129, 0.3);
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 600;
  color: #059669;
  animation: hintPulse 2s ease-in-out infinite;
}

.question-hint svg {
  width: 18px;
  height: 18px;
  flex-shrink: 0;
}

@keyframes shakeX {
  from,
  to {
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
  }

  10%,
  30%,
  50%,
  70%,
  90% {
    -webkit-transform: translate3d(-10px, 0, 0);
    transform: translate3d(-10px, 0, 0);
  }

  20%,
  40%,
  60%,
  80% {
    -webkit-transform: translate3d(10px, 0, 0);
    transform: translate3d(10px, 0, 0);
  }
}

.animate__shakeX {
  -webkit-animation-name: shakeX;
  animation-name: shakeX;
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}

@keyframes hintPulse {
  0%, 100% {
    transform: scale(1);
    box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.2);
  }
  50% {
    transform: scale(1.02);
    box-shadow: 0 0 0 4px rgba(16, 185, 129, 0.1);
  }
}

/* Options Grid */
.options-grid {
  display: grid;
  gap: 1rem;
  margin-top: 2rem;
}

.option-card {
  position: relative;
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 1.25rem 1.5rem;
  background: white;
  border: 2.5px solid #e2e8f0;
  border-radius: 16px;
  cursor: pointer;
  transition: all 0.3s ease;
  overflow: hidden;
}

.option-card::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 4px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  transform: scaleY(0);
  transition: transform 0.3s ease;
}

.option-card:hover {
  border-color: #667eea;
  transform: translateX(4px);
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.15);
}

.option-selected {
  border-color: #667eea;
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.08) 0%, rgba(118, 75, 162, 0.08) 100%);
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.2);
}

.option-radio {
  flex-shrink: 0;
}

.radio-outer {
  width: 24px;
  height: 24px;
  border: 2.5px solid #cbd5e0;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.option-card:hover .radio-outer {
  border-color: #667eea;
}

.option-selected .radio-outer {
  border-color: #667eea;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.radio-inner {
  width: 10px;
  height: 10px;
  background: white;
  border-radius: 50%;
  animation: radioScale 0.3s ease;
}

@keyframes radioScale {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}

/* Checkbox Styles */
.option-checkbox {
  flex-shrink: 0;
}

.checkbox-outer {
  width: 24px;
  height: 24px;
  border: 2.5px solid #cbd5e0;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.option-card:hover .checkbox-outer {
  border-color: #667eea;
}

.option-selected .checkbox-outer {
  border-color: #667eea;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.checkbox-inner {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  animation: checkboxScale 0.3s ease;
}

.checkbox-inner svg {
  width: 16px;
  height: 16px;
}

@keyframes checkboxScale {
  0% {
    transform: scale(0) rotate(-45deg);
  }
  50% {
    transform: scale(1.2) rotate(0deg);
  }
  100% {
    transform: scale(1) rotate(0deg);
  }
}

.option-label {
  flex: 1;
  font-size: 1.05rem;
  font-weight: 500;
  color: #2d3748;
  transition: color 0.3s ease;
}

.option-selected .option-label {
  color: #667eea;
  font-weight: 600;
}

.option-check svg {
  width: 100%;
  height: 100%;
}

/* Footer */
.quiz-footer {
  padding: 0 2.5rem 2.5rem;
}

.start-button-container {
  display: flex;
  justify-content: center;
}

.btn-start {
  display: flex;
  align-items: center;
  gap: 1rem;
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  color: white;
  border: none;
  padding: 1.5rem 2rem;
  border-radius: 16px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  box-shadow: 0 10px 30px rgba(16, 185, 129, 0.4);
  transition: all 0.3s ease;
  max-width: 600px;
  width: 100%;
}

.btn-start:hover {
  transform: translateY(-2px);
  box-shadow: 0 15px 40px rgba(16, 185, 129, 0.5);
}

.btn-start:active {
  transform: translateY(0);
}

.btn-icon {
  font-size: 2rem;
  animation: bounce 2s infinite;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-8px);
  }
}

.btn-content {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  text-align: left;
}

.btn-main-text {
  font-size: 1.1rem;
  font-weight: 700;
}

.btn-sub-text {
  font-size: 0.85rem;
  opacity: 0.95;
  margin-top: 0.25rem;
}

/* Navigation Buttons */
.navigation-buttons {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

.btn-nav {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 1rem 2rem;
  border: none;
  border-radius: 12px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  /*  transition: all 0.3s ease;*/
  min-width: 140px;
  justify-content: center;
}

.btn-nav svg {
  width: 24px;
  height: 24px;
}

.btn-back {
  background: #f7fafc;
  color: #4a5568;
  border: 2px solid #e2e8f0;
}

.btn-back:hover {
  background: #edf2f7;
  border-color: #cbd5e0;
}

.btn-next {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
}

.btn-next:hover {
  box-shadow: 0 10px 25px rgba(102, 126, 234, 0.5);
}


/* Transitions */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

.slide-fade-enter-active {
  transition: all 0.4s ease;
}

.slide-fade-leave-active {
  transition: all 0.4s ease;
  position: absolute;
}

.slide-fade-enter-from {
  transform: translateX(30px);
  opacity: 0;
}

.slide-fade-leave-to {
  transform: translateX(-30px);
  opacity: 0;
}

/* Large Screens */
@media (min-width: 1200px) {
  .quiz-container {
    min-width: 800px;
  }
}

/* Responsive Design */
@media (max-width: 768px) {
  .quiz-wrapper {
    padding: 0.75rem;
  }

  .quiz-container {
    border-radius: 16px;
  }

  .question-container {
    padding: 1rem;
    min-height: 350px;
  }

  .question-title {
    font-size: 1.4rem;
  }

  .option-card {
    padding: 1rem;
  }

  .option-label {
    font-size: 0.95rem;
  }

  .quiz-footer {
    padding: 0 1.5rem 1.5rem;
  }

  .btn-start {
    padding: 1.25rem 1.5rem;
    font-size: 0.9rem;
  }

  .btn-main-text {
    font-size: 1rem;
  }

  .btn-sub-text {
    font-size: 0.8rem;
  }

  .btn-nav {
    padding: 0.875rem 1.5rem;
    font-size: 1rem;
    min-width: 110px;
  }

  .progress-bar-container {
    padding: 1.5rem 1.5rem 0.75rem;
  }
}

@media (max-width: 480px) {
  .question-title {
    font-size: 1.2rem;
  }

  .option-card {
    padding: 0.875rem;
  }

  .btn-content {
    align-items: center;
    text-align: center;
  }
}
</style>