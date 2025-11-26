<script setup>

</script>

<template>
  <div class="quiz-start">
    <div class="welcome-badge">
      <span class="badge-icon">✨</span>
      <span>Узнайте стоимость за 1 минуту</span>
    </div>

    <h1 class="main-title">
      Пройдите тест и узнайте стоимость вашего будущего потолка прямо сейчас
    </h1>

    <p class="subtitle">
      Ответьте на несколько простых вопросов и получите предварительный расчёт стоимости
    </p>

    <div class="benefits-grid">
      <div class="benefit-card">
        <div class="benefit-content">
          <h3>15 лет гарантии</h3>
          <p>На материал и работу</p>
        </div>
      </div>

      <div class="benefit-card">
        <div class="benefit-content">
          <h3>Бесплатный выезд</h3>
          <p>Замерщика на объект</p>
        </div>
      </div>

      <div class="benefit-card">
        <div class="benefit-content">
          <h3>Гарантированный подарок</h3>
          <p>За прохождение теста</p>
        </div>
      </div>
    </div>

    <div class="stats-container">
      <div class="stat-item">
        <div class="stat-number">5</div>
        <div class="stat-label">вопросов</div>
      </div>
      <div class="stat-divider"></div>
      <div class="stat-item">
        <div class="stat-number">1</div>
        <div class="stat-label">минута</div>
      </div>
      <div class="stat-divider"></div>
      <div class="stat-item">
        <div class="stat-number">🎁</div>
        <div class="stat-label">подарок</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.quiz-start {
  text-align: center;
}

.welcome-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: linear-gradient(135deg, #ffd89b 0%, #19547b 100%);
  color: white;
  padding: 0.6rem 1.5rem;
  border-radius: 30px;
  font-weight: 600;
  font-size: 0.95rem;
  margin-bottom: 1.5rem;
  box-shadow: 0 4px 15px rgba(25, 84, 123, 0.3);
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.03);
  }
}

.badge-icon {
  font-size: 1.2rem;
  animation: rotate 3s linear infinite;
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.main-title {
  font-size: 2.25rem;
  font-weight: 800;
  color: #1a202c;
  line-height: 1.3;
  margin: 0 0 1rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  font-size: 1.1rem;
  color: #4a5568;
  margin: 0 0 2.5rem;
  line-height: 1.6;
}

.benefits-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1.25rem;
  margin-bottom: 2.5rem;
}

.benefit-card {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
  padding: 1.5rem;
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.05) 0%, rgba(118, 75, 162, 0.05) 100%);
  border: 2px solid rgba(102, 126, 234, 0.1);
  border-radius: 16px;
  text-align: left;
  transition: all 0.3s ease;
}

.benefit-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.15);
  border-color: rgba(102, 126, 234, 0.3);
}

.benefit-icon {
  flex-shrink: 0;
  width: 48px;
  height: 48px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.benefit-icon svg {
  width: 24px;
  height: 24px;
}

.benefit-content h3 {
  margin: 0 0 0.25rem;
  font-size: 1.05rem;
  font-weight: 700;
  color: #2d3748;
}

.benefit-content p {
  margin: 0;
  font-size: 0.9rem;
  color: #718096;
}

.stats-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 2rem;
  padding: 1.5rem;
  background: white;
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
}

.stat-item {
  text-align: center;
}

.stat-number {
  font-size: 2.5rem;
  font-weight: 800;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  line-height: 1;
  margin-bottom: 0.5rem;
}

.stat-label {
  font-size: 0.9rem;
  color: #718096;
  font-weight: 600;
}

.stat-divider {
  width: 2px;
  height: 40px;
  background: linear-gradient(180deg, transparent, #e2e8f0, transparent);
}

@media (max-width: 768px) {
  .main-title {
    font-size: 1.75rem;
  }

  .subtitle {
    font-size: 1rem;
  }

  .benefits-grid {
    gap: 1rem;
  }

  .benefit-card {
    padding: 1.25rem;
  }

  .stats-container {
    gap: 1.5rem;
    padding: 1.25rem;
  }

  .stat-number {
    font-size: 2rem;
  }
}

@media (max-width: 480px) {
  .main-title {
    font-size: 1.5rem;
  }

  .benefits-grid {
    grid-template-columns: 1fr;
  }

  .stats-container {
    gap: 1rem;
  }

  .stat-number {
    font-size: 1.75rem;
  }

  .stat-label {
    font-size: 0.8rem;
  }
}
</style>