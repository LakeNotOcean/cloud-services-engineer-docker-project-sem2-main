```markdown
# Momo Store Frontend

Docker-образ для SPA-приложения на Vue.js с веб-сервером nginx.

## Сборка образа

```bash
docker build -t momo-frontend:latest .
```

При необходимости указания переменных окружения сборки:

```bash
docker build \
  --build-arg VUE_APP_API_URL=https://api.example.com \
  --build-arg BASE_URL=/momo-store \
  -t momo-frontend:latest .
```

## Запуск контейнера

```bash
docker run -d -p 80:80 --name momo-frontend momo-frontend:latest
```

Приложение доступно по адресу 

```
http://localhost:80/momo-store
```

## Использованные технологические решения

- Для сборки используется официальный образ `node:26-alpine`.
- Финальный образ основан на `nginxinc/nginx-unprivileged:1.29.8-alpine` и содержит только статические файлы и конфигурацию nginx.
- Итоговый образ не содержит Node.js, npm, исходный код приложения и сборочные зависимости.
- Статические файлы принадлежат пользователю `appuser` и доступны nginx только для чтения.
- Основной процесс nginx запускается от непривилегированного пользователя `nginx`. Права `root` используются на этапе сборки только для установки пакетов и настройки прав.
- Для проверки работоспособности используется `HEALTHCHECK` с `wget` к локальному эндпоинту.

### Размер образа
> Точный размер будет указан после сборки.  
> Ожидаемый результат: **< 90MB**.

# Режим разработки (dev окружение)

Для локальной разработки также существует отдельная стадия dev в Dockerfile, которая запускает Webpack Dev Server.

## Сборка 

```bash
docker build --target dev -t momo-frontend:dev .
```

При необходимости можно также передать переменные окружения:
```bash
docker build \
  --target dev \
  --build-arg VUE_APP_API_URL=http://localhost:3000/api \
  --build-arg BASE_URL=/ \
  -t momo-frontend:dev .
```


## Запуск dev-контейнера с монтированием исходников

Для осуществления HMW:

```bash
docker run -d \
  -p 8080:8080 \
  -v $(pwd):/app \
  -v /app/node_modules \
  -e NODE_OPTIONS="--openssl-legacy-provider" \
  --name momo-frontend-dev \
  momo-frontend:dev
```

