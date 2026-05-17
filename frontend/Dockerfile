# установка зависимостей с помощью npm
FROM node:26-alpine AS base

WORKDIR /app

COPY package*.json ./
RUN npm ci && npm cache clean --force

# стенд для dev-сервера
FROM base AS dev

# аргументы для сборки и перпеменные окружения
ARG VUE_APP_API_URL=/api
ENV VUE_APP_API_URL=$VUE_APP_API_URL
ARG BASE_URL=/
ENV BASE_URL=$BASE_URL

WORKDIR /app

COPY . .

EXPOSE 8080

CMD ["npm", "run", "serve", "--", "--host", "0.0.0.0"]

# сборка приложения для prod
FROM base AS builder

# аргументы для сборки и перпеменные окружения
ARG VUE_APP_API_URL=/api
ENV VUE_APP_API_URL=$VUE_APP_API_URL
ARG BASE_URL=/
ENV BASE_URL=$BASE_URL

ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV

# для совместимости со старым Webpack
ENV NODE_OPTIONS=--openssl-legacy-provider

# установка git (может потребоваться)
RUN apk add --no-cache git ca-certificates

WORKDIR /app

COPY . .

RUN npm run build

FROM nginxinc/nginx-unprivileged:1.29.8-alpine AS prod

USER root

LABEL maintainer="ch3shirsins@yandex.ru" \
    description="Cloud Services Engineer Docker Project Sem2 Vue-nginx Frontend" \
    version="1.0.0" \
    security.capabilities="minimal"

RUN apk add --no-cache wget 

# запуск от пользователя appuser
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=builder --chown=appuser:appgroup /app/dist /usr/share/nginx/html/momo-store
COPY --chown=appuser:appgroup public/_redirects /usr/share/nginx/html/momo-store/_redirects
COPY --chown=appuser:appgroup nginx.conf /etc/nginx/conf.d/default.conf

RUN chmod -R 755 /usr/share/nginx/html/momo-store

USER nginx

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost/momo-store/ || exit 1

CMD ["nginx", "-g", "daemon off;"]