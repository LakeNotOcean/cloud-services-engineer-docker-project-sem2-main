FROM golang:1.26-alpine AS builder

RUN apk add --no-cache git ca-certificates

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .

ARG TARGETOS=linux
ARG TARGETARCH=amd64
RUN CGO_ENABLED=0 GOOS=${TARGETOS} GOARCH=${TARGETARCH} \
    go build -ldflags="-w -s" -o /api ./cmd/api

# Финальный этап
FROM alpine:3.22.4

RUN apk add --no-cache wget

LABEL maintainer="ch3shirsins@yandex.ru" \
    description="Cloud Services Engineer Docker Project Sem2 Golang Backend" \
    version="1.0.0" \
    security.capabilities="minimal"

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --chown=appuser:appgroup --from=builder /api /api

USER appuser
WORKDIR /app

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:8081/health || exit 1

CMD ["/api"]