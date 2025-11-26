<script setup>
import {ref, watch} from "vue";

const props = defineProps({
  userData: Object,
  isSubmitting: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['submit'])

const errors = ref([])

watch(() => props.userData.name, (newValue) => {
  if (newValue && hasError('name')) {
    removeError('name')
  }
})

watch(() => props.userData.phone, (newValue) => {
  if (newValue && hasError('phone')) {
    removeError('phone')
  }
})

const handleSubmit = () => {
  if (props.isSubmitting) return;

  errors.value = []

  if (!props.userData.name) {
    errors.value.push({
      field: 'name',
      message: 'Поле имя обязательно к заполнению'
    })
  }

  if (!props.userData.phone) {
    errors.value.push({
      field: 'phone',
      message: 'Поле телефон обязательно к заполнению'
    })
  }

  if (!props.userData.name || !props.userData.phone) {
    return;
  }

  emit('submit');
}

const hasError = (fieldName) => {
  return errors.value.some(error => error.field === fieldName)
}

const getError = (fieldName) => {
  const error = errors.value.find(error => error.field === fieldName)
  return error ? error.message : ''
}

const removeError = (fieldName) => {
  errors.value = errors.value.filter(error => error.field !== fieldName)
}
</script>

<template>
  <div class="user-form">
    <div class="form-header">
      <div class="success-icon">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
          <path
              d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
        </svg>
      </div>
      <h2 class="form-title">Отлично! Вы прошли тест</h2>
      <p class="form-subtitle">Оставьте свои контакты, и мы рассчитаем точную стоимость вашего потолка</p>
    </div>

    <div class="form-content">
      <div class="form-group">
        <label class="form-label">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
            <path
                d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/>
          </svg>
          Ваше имя
        </label>
        <input
            v-model.trim="userData.name"
            type="text"
            placeholder="Введите ваше имя"
            class="form-input"
            :class="{ 'error': hasError('name') }"
            required
        >
        <div v-if="hasError('name')" class="error-message">
          {{ getError('name') }}
        </div>
      </div>

      <div class="form-group">
        <label class="form-label">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
            <path
                d="M6.62 10.79c1.44 2.83 3.76 5.14 6.59 6.59l2.2-2.2c.27-.27.67-.36 1.02-.24 1.12.37 2.33.57 3.57.57.55 0 1 .45 1 1V20c0 .55-.45 1-1 1-9.39 0-17-7.61-17-17 0-.55.45-1 1-1h3.5c.55 0 1 .45 1 1 0 1.25.2 2.45.57 3.57.11.35.03.74-.25 1.02l-2.2 2.2z"/>
          </svg>
          Номер телефона
        </label>
        <input
            v-model.trim="userData.phone"
            type="tel"
            placeholder="+7 (___) ___-__-__"
            class="form-input"
            :class="{ 'error': hasError('phone') }"
            required
        >
        <div v-if="hasError('phone')" class="error-message">
          {{ getError('phone') }}
        </div>
      </div>

      <button class="submit-button" @click="handleSubmit" :disabled="isSubmitting"
              :class="{ 'is-loading': isSubmitting }">
        <span class="button-icon" v-show="!isSubmitting">🎉</span>
        <div class="spinner" v-show="isSubmitting"></div>
        <span>{{ isSubmitting ? 'Отправка...' : 'Получить расчёт стоимости' }}</span>
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="button-arrow"
             v-show="!isSubmitting">
          <path d="M8.59 16.59L10 18l6-6-6-6-1.41 1.41L13.17 12z"/>
        </svg>
      </button>

      <p class="privacy-notice">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
          <path
              d="M12 1L3 5v6c0 5.55 3.84 10.74 9 12 5.16-1.26 9-6.45 9-12V5l-9-4zm0 10.99h7c-.53 4.12-3.28 7.79-7 8.94V12H5V6.3l7-3.11v8.8z"/>
        </svg>
        Нажимая кнопку, вы соглашаетесь с политикой конфиденциальности
      </p>
    </div>

    <div class="bonus-banner">
      <div class="bonus-icon">🎁</div>
      <div class="bonus-text">
        <strong>Ваш подарок уже ждёт вас!</strong>
        <span>Получите его после расчёта стоимости</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.user-form {
  animation: fadeInScale 0.5s ease-out;
}

@keyframes fadeInScale {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.form-header {
  text-align: center;
  margin-bottom: 2.5rem;
}

.success-icon {
  width: 80px;
  height: 80px;
  margin: 0 auto 1.5rem;
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3);
  animation: successPulse 2s ease-in-out infinite;
}

@keyframes successPulse {
  0%, 100% {
    transform: scale(1);
    box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3);
  }
  50% {
    transform: scale(1.05);
    box-shadow: 0 15px 40px rgba(16, 185, 129, 0.4);
  }
}

.success-icon svg {
  width: 48px;
  height: 48px;
}

.form-title {
  font-size: 2rem;
  font-weight: 800;
  color: #1a202c;
  margin: 0 0 0.75rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.form-subtitle {
  font-size: 1.05rem;
  color: #4a5568;
  margin: 0;
  line-height: 1.6;
}

.form-content {
  margin-bottom: 2rem;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-weight: 600;
  color: #2d3748;
  margin-bottom: 0.75rem;
  font-size: 0.95rem;
}

.form-label svg {
  width: 20px;
  height: 20px;
  color: #667eea;
}

.form-input {
  width: 100%;
  padding: 1rem 1.25rem;
  font-size: 1rem;
  border: 2.5px solid #e2e8f0;
  border-radius: 12px;
  transition: all 0.3s ease;
  background: white;
  color: #2d3748;
  outline: none;
}

.form-input:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 4px rgba(102, 126, 234, 0.1);
  transform: translateY(-2px);
}

.form-input::placeholder {
  color: #a0aec0;
}

.form-input.error {
  border-color: #fd7171;
  box-shadow: 0 0 0 4px rgb(234 102 102 / 19%);
}

.error-message {
  color: #ff4444;
  font-size: 12px;
  margin-top: 4px;
}

.error-message::before {
  font-size: 14px;
}

.submit-button {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  padding: 1.25rem 2rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 1.1rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
  position: relative;
  overflow: hidden;
}

.submit-button::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
  transition: left 0.5s ease;
}

.submit-button:hover::before {
  left: 100%;
}

.submit-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 15px 40px rgba(102, 126, 234, 0.5);
}

.submit-button:active {
  transform: translateY(0);
}

.submit-button:disabled {
  cursor: not-allowed;
  opacity: 0.8;
}

.submit-button.is-loading {
  pointer-events: none;
}

.submit-button.is-loading:hover {
  transform: none;
}

.button-icon {
  font-size: 1.5rem;
  animation: bounceIcon 2s infinite;
}

@keyframes bounceIcon {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-5px);
  }
}

.spinner {
  width: 24px;
  height: 24px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-top-color: white;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.button-arrow {
  width: 24px;
  height: 24px;
  transition: transform 0.3s ease;
}

.submit-button:hover .button-arrow {
  transform: translateX(4px);
}

.privacy-notice {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  margin-top: 1rem;
  font-size: 0.8rem;
  color: #718096;
  text-align: center;
  line-height: 1.5;
}

.privacy-notice svg {
  width: 16px;
  height: 16px;
  flex-shrink: 0;
  color: #667eea;
}

.bonus-banner {
  display: flex;
  align-items: center;
  gap: 1.25rem;
  padding: 1.5rem;
  background: linear-gradient(135deg, rgba(16, 185, 129, 0.1) 0%, rgba(5, 150, 105, 0.1) 100%);
  border: 2px solid rgba(16, 185, 129, 0.3);
  border-radius: 16px;
  animation: glow 2s ease-in-out infinite;
}

@keyframes glow {
  0%, 100% {
    box-shadow: 0 0 20px rgba(16, 185, 129, 0.2);
  }
  50% {
    box-shadow: 0 0 30px rgba(16, 185, 129, 0.4);
  }
}

.bonus-icon {
  font-size: 3rem;
  flex-shrink: 0;
  animation: rotateGift 3s linear infinite;
}

@keyframes rotateGift {
  0%, 90%, 100% {
    transform: rotate(0deg);
  }
  95% {
    transform: rotate(15deg);
  }
  97% {
    transform: rotate(-15deg);
  }
}

.bonus-text {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.bonus-text strong {
  font-size: 1.1rem;
  color: #065f46;
}

.bonus-text span {
  font-size: 0.9rem;
  color: #047857;
}

@media (max-width: 768px) {
  .form-title {
    font-size: 1.6rem;
  }

  .form-subtitle {
    font-size: 0.95rem;
  }

  .success-icon {
    width: 70px;
    height: 70px;
  }

  .success-icon svg {
    width: 40px;
    height: 40px;
  }

  .submit-button {
    padding: 1.125rem 1.5rem;
    font-size: 1rem;
  }

  .bonus-banner {
    padding: 1.25rem;
  }

  .bonus-icon {
    font-size: 2.5rem;
  }
}

@media (max-width: 480px) {
  .form-title {
    font-size: 1.4rem;
  }

  .bonus-text strong {
    font-size: 1rem;
  }

  .bonus-text span {
    font-size: 0.85rem;
  }
}
</style>