# 🚀 Руководство по деплою на Render.com

## ✅ Проект готов к деплою!

Все исправления загрузки файлов внесены и готовы к продакшн использованию.

---

## 📦 Что было исправлено

### 1. **Прикрепление файлов** ✅
- ✅ Контроллер принимает файлы (`files: []`)
- ✅ Форма правильно отправляет файлы (`message[files][]`)
- ✅ Отображение файлов в сообщениях (изображения, текст, документы)
- ✅ AI обработка: изображения → vision, текст → контекст

### 2. **База данных** ✅
- ✅ PostgreSQL настроен через переменные окружения
- ✅ Конфигурация готова для Render

### 3. **Конфигурация** ✅
- ✅ Dockerfile готов
- ✅ railway.toml (подходит и для Render)
- ✅ ActiveStorage настроен

---

## 🎯 Деплой на Render.com за 5 минут

### Шаг 1: Загрузите код на GitHub

```bash
# Инициализируйте git репозиторий (если еще не сделано)
git init
git add .
git commit -m "Initial commit with file upload fixes"

# Создайте репозиторий на GitHub и загрузите код
git remote add origin https://github.com/ваш-username/ваш-репо.git
git push -u origin main
```

### Шаг 2: Создайте новый Web Service на Render

1. Зайдите на [render.com](https://render.com) и войдите
2. Нажмите **"New +"** → **"Web Service"**
3. Подключите ваш GitHub репозиторий
4. Render автоматически обнаружит Dockerfile

### Шаг 3: Настройте сервис

**Основные настройки:**
- **Name**: `your-app-name`
- **Region**: выберите ближайший регион
- **Branch**: `main`
- **Runtime**: `Docker`
- **Instance Type**: выберите план (Free для начала)

### Шаг 4: Добавьте базу данных

1. Нажмите **"New +"** → **"PostgreSQL"**
2. Выберите **Free** или платный план
3. Дождитесь создания БД
4. Скопируйте **Internal Database URL**

### Шаг 5: Настройте Environment Variables

В настройках Web Service добавьте:

```bash
# База данных (Internal Database URL из предыдущего шага)
DATABASE_URL=postgresql://user:pass@host/dbname

# Rails Secret (сгенерируйте: rails secret)
SECRET_KEY_BASE=ваш_секретный_ключ_длиной_минимум_30_символов

# Публичный хост
PUBLIC_HOST=your-app-name.onrender.com

# LLM Configuration (для AI чата)
LLM_BASE_URL=https://api.openai.com/v1
LLM_API_KEY=sk-ваш-api-ключ
LLM_MODEL=gpt-4o-mini

# Email (опционально)
EMAIL_SMTP_ADDRESS=smtp.gmail.com
EMAIL_SMTP_PORT=587
EMAIL_SMTP_USERNAME=your-email@gmail.com
EMAIL_SMTP_PASSWORD=your-app-password
EMAIL_SMTP_DOMAIN=gmail.com

# Rails Environment
RAILS_ENV=production
RAILS_LOG_TO_STDOUT=enabled
RAILS_SERVE_STATIC_FILES=enabled
```

### Шаг 6: Deploy!

Нажмите **"Create Web Service"** - Render автоматически:
- 📦 Соберет Docker образ
- 🗄️ Создаст и настроит БД
- 🚀 Запустит приложение
- 🌐 Выдаст публичный URL

---

## 🔧 Важные команды для настройки

После первого деплоя выполните через Render Shell:

```bash
# Создайте таблицы БД
rails db:migrate

# Создайте админа
rails runner "Administrator.create!(email: 'admin@example.com', password: 'admin123', name: 'Admin')"

# Проверьте статус
rails runner "puts User.count"
```

---

## 📁 Файловое хранилище (для загрузки файлов)

### Вариант 1: Использовать Render Disk (проще)

В `config/storage.yml` уже настроен `:local`:

```yaml
local:
  service: Disk
  root: <%= Rails.root.join("storage") %>
```

**Добавьте Persistent Disk в Render:**
1. В настройках Web Service → **"Disks"**
2. Добавьте новый диск: 
   - Mount Path: `/rails/storage`
   - Size: 10GB (или больше)

### Вариант 2: Использовать AWS S3 (рекомендуется для продакшн)

1. Создайте S3 bucket на AWS
2. Добавьте gem в Gemfile:
```ruby
gem 'aws-sdk-s3', require: false
```

3. Обновите `config/storage.yml`:
```yaml
amazon:
  service: S3
  access_key_id: <%= ENV['AWS_ACCESS_KEY_ID'] %>
  secret_access_key: <%= ENV['AWS_SECRET_ACCESS_KEY'] %>
  region: us-east-1
  bucket: your-bucket-name
```

4. В `config/environments/production.rb`:
```ruby
config.active_storage.service = :amazon
```

5. Добавьте переменные окружения в Render:
```bash
AWS_ACCESS_KEY_ID=ваш_ключ
AWS_SECRET_ACCESS_KEY=ваш_секрет
```

---

## 🔍 Проверка работоспособности

После деплоя протестируйте:

### 1. Откройте приложение
```
https://your-app-name.onrender.com
```

### 2. Проверьте авторизацию через Telegram
- Должна работать Telegram Web App авторизация

### 3. Протестируйте загрузку файлов
1. Создайте чат
2. Прикрепите изображение
3. Прикрепите текстовый файл
4. Отправьте сообщение
5. Проверьте, что AI получил файлы и ответил

### 4. Проверьте админку
```
https://your-app-name.onrender.com/admin
Login: admin@example.com
Password: admin123 (или что вы установили)
```

---

## 📊 Мониторинг

Render предоставляет:
- 📈 **Метрики**: CPU, Memory, Response time
- 📝 **Логи**: Real-time logs
- ⚠️ **Алерты**: Email notifications
- 🔄 **Auto-deploy**: на каждый push в GitHub

---

## 💰 Стоимость

### Free Plan:
- ✅ 750 часов в месяц
- ✅ PostgreSQL 90 дней
- ✅ Автоматические деплои
- ⚠️ Спит после 15 мин неактивности

### Starter Plan ($7/month):
- ✅ Без сна
- ✅ Больше ресурсов
- ✅ Custom domains
- ✅ Priority support

---

## 🐛 Решение проблем

### Ошибка миграции БД:
```bash
# Выполните через Render Shell:
rails db:migrate:reset
rails db:seed
```

### Файлы не загружаются:
- Проверьте Persistent Disk в настройках
- Или настройте S3

### AI не отвечает:
- Проверьте `LLM_API_KEY` в Environment Variables
- Проверьте баланс API ключа
- Проверьте логи: `rails runner "puts LlmService.new(prompt: 'test').call"`

### Ошибка авторизации Telegram:
- Убедитесь, что `PUBLIC_HOST` правильно настроен
- Проверьте Telegram Bot настройки

---

## 🎉 Готово!

Теперь ваше приложение работает в продакшн с полной поддержкой:
- ✅ Загрузка и отображение файлов
- ✅ AI обработка изображений и текста
- ✅ Telegram авторизация
- ✅ Автоматические деплои
- ✅ Масштабирование

---

## 📞 Поддержка

- **Render Docs**: https://render.com/docs
- **Rails Guides**: https://guides.rubyonrails.org
- **ActiveStorage**: https://edgeguides.rubyonrails.org/active_storage_overview.html

Удачи с деплоем! 🚀
