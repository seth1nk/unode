<template>
  <header>
    <div class="wrapper">
      <router-view></router-view>
    </div>
    <link href="https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@400;700&display=swap" rel="stylesheet">
  </header>
  <main>
    <TheWelcome />
  </main>
  <aside class="right-panel">
    <div class="logo-container">
      <router-link to="/">
        <img src="/images/logo.png" alt="Logo" class="logo" />
      </router-link>
    </div>
    <nav class="nav-buttons">
      <ul class="button-list">
        <li><router-link to="/" class="button">Главная</router-link></li>
        <li><router-link to="/about" class="button">О нас</router-link></li>
        <li><router-link to="/contact" class="button">Контакты</router-link></li>
        <li v-if="isAuthenticated"><router-link to="/smartwatches" class="button">Смарт-часы</router-link></li>
        <li v-if="isAuthenticated"><router-link to="/clients" class="button">Клиенты</router-link></li>
        <li v-if="!isAuthenticated"><router-link to="/login" class="button">Войти</router-link></li>
        <li v-if="!isAuthenticated"><router-link to="/register" class="button">Зарегистрироваться</router-link></li>
        <li v-if="isAuthenticated" class="welcome-message"><span>Добро пожаловать, {{ username }}</span></li>
        <li v-if="isAuthenticated"><button @click="logout" class="button">Выйти</button></li>
        <li v-if="isAuthenticated && role === 'admin'"><a href="https://uvue.onrender.com" class="admin-button">Админ-панель</a></li>
      </ul>
    </nav>
  </aside>
</template>

<script>
export default {
  data() {
    return {
      isAuthenticated: false,
      username: '',
      role: ''
    };
  },
  created() {
    this.checkAuth();
  },
  methods: {
    async checkAuth() {
      try {
        const response = await fetch('https://uvue.onrender.com/auth/check', {
          method: 'GET',
          credentials: 'include',
        });
        const data = await response.json();

        if (response.ok) {
          this.isAuthenticated = true;
          this.username = data.username || 'Пользователь';
          this.role = data.role || '';
        } else {
          console.error('Токен недействителен:', data.message);
          this.isAuthenticated = false;
          this.username = '';
          this.role = '';
        }
      } catch (err) {
        console.error('Ошибка проверки авторизации:', err);
        this.isAuthenticated = false;
        this.username = '';
        this.role = '';
      }
    },
    async logout() {
      try {
        const response = await fetch('https://uvue.onrender.com/auth/logout', {
          method: 'GET',
          credentials: 'include',
        });

        if (response.ok) {
          this.isAuthenticated = false;
          this.username = '';
          this.role = '';
          this.$router.push('/');
        } else {
          console.error('Сервер вернул ошибку при выходе');
        }
      } catch (err) {
        console.error('Ошибка выхода:', err);
        this.isAuthenticated = false;
        this.username = '';
        this.role = '';
        this.$router.push('/');
      }
    }
  }
};
</script>

<style scoped>
/* Боковая панель */
.right-panel {
  position: fixed;
  top: 50%;
  right: 0;
  transform: translateY(-50%);
  background: rgba(26, 32, 44, 0.95); /* Тёмный прозрачный фон */
  backdrop-filter: blur(10px);
  border-left: 1px solid rgba(255, 255, 255, 0.3); /* Белая граница */
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

/* Логотип */
.logo-container {
  display: flex;
  justify-content: center;
}

.logo {
  max-width: 100px;
  height: auto;
  border-radius: 5px;
  transition: transform 0.3s ease;
}

.logo:hover {
  transform: scale(1.05);
}

/* Список кнопок */
.button-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

/* Общие стили для кнопок навигации */
.button {
  display: block;
  width: 180px;
  padding: 10px 15px;
  background: #ff6b6b; /* Яркий розовый фон */
  color: #ffffff; /* Белый текст */
  text-decoration: none;
  border-radius: 8px;
  font-weight: bold;
  text-align: center;
  transition: background 0.3s ease, transform 0.3s ease, box-shadow 0.3s ease;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}

.button:hover {
  background: #4b0082; /* Тёмный фиолетовый при ховере */
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
}

/* Кнопка админ-панели */
.admin-button {
  display: block;
  width: auto;
  padding: 10px 15px;
  background: linear-gradient(135deg, #ff6b6b, #4b0082); /* Градиент розовый-фиолетовый */
  color: #ffffff;
  text-decoration: none;
  border-radius: 8px;
  font-weight: bold;
  text-transform: uppercase;
  text-align: center;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  transition: background 0.3s ease, transform 0.3s ease, box-shadow 0.3s ease;
}

.admin-button:hover {
  background: linear-gradient(135deg, #4b0082, #ff6b6b);
  transform: translateY(-2px);
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
}

/* Приветственное сообщение */
.welcome-message span {
  display: block;
  width: auto;
  padding: 10px 15px;
  font-size: 16px;
  color: #ffffff; /* Белый текст */
  font-weight: bold;
  text-align: center;
  background: rgba(255, 255, 255, 0.2); /* Полупрозрачный белый фон */
  border-radius: 8px;
}

/* Адаптивность */
@media (max-width: 768px) {
  .right-panel {
    padding: 15px;
    width: 160px;
  }

  .logo {
    max-width: 80px;
  }

  .button {
    width: 140px;
    padding: 8px 10px;
    font-size: 0.9rem;
  }

  .admin-button,
  .welcome-message span {
    padding: 8px 10px;
    font-size: 0.9rem;
  }
}
</style>
