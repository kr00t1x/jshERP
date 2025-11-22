# 构建并发布前端静态资源
FROM node:20.17.0-alpine AS build
WORKDIR /app
COPY jshERP-web/ ./
RUN npm config set legacy-peer-deps true \
    && npm install --registry=https://registry.npmmirror.com \
    && npm run build

FROM nginx:1.26-alpine
COPY docker/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
