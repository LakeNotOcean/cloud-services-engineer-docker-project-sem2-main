# Momo Store

Учебный проект, демонстрирующий контейнеризацию веб‑приложения с использованием Docker и Docker Compose.

Проект включает:
- Backend. HTTP API на Go (порт 8081 внутри контейнера).
- Frontend. SPA на Vue.js и nginx (порт 80 внутри контейнера). Создается 2 реплики.
- Reverse Proxy. Nginx, который объединяет фронтенд и бэкенд под единым портом 80.

# Запуск проекта

```
docker compose --profile prod up -d
```
После запуска приложение доступно на http://localhost.

# Особенности окружения разработки

Запуск осуществляется командой:

```
docker compose -f docker-compose.yml -f docker-compose.dev.yml --profile dev up -d
```

Окружение разработки не включается в себя прокси, реплики и использует Webpack Dev Server, который следит за изменениями исходного кода и автоматически пересобирает приложение.

# Подробнее о сборке отдельных образов и запуске контейнеров

- [Backend](./backend/readme.md)
- [Frontend](./frontend/readme.md)