# ── Etapa 1: Build con Node ───────────────────────────────────────────
FROM node:20-alpine AS build
WORKDIR /app

# Copiar dependencias primero (caché de capas)
COPY package*.json ./
RUN npm ci --silent

# Copiar código y compilar
COPY . .
RUN npm run build

# ── Etapa 2: Servir con Nginx (imagen mínima) ─────────────────────────
FROM nginx:alpine

# Crear usuario no root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copiar build estático
COPY --from=build /app/dist /usr/share/nginx/html

# Configuración nginx para SPA (React Router)
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Ajustar permisos
RUN chown -R appuser:appgroup /usr/share/nginx/html && \
    chown -R appuser:appgroup /var/cache/nginx && \
    chown -R appuser:appgroup /var/run && \
    chown -R appuser:appgroup /etc/nginx/conf.d && \
    chown -R appuser:appgroup /var/log/nginx && \
    touch /var/run/nginx.pid && \
    chown appuser:appgroup /var/run/nginx.pid

USER appuser

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
