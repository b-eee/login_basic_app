FROM node:lts-alpine as builder
ARG ENV=develop
RUN apk update && apk add --no-cache make git nodejs
WORKDIR /app
COPY . /app/
COPY front/.env_${ENV} .env
RUN apk add --no-cache --virtual .gyp \
        python \
        make \
        g++ \
    && npm install \
    && apk del .gyp

RUN npm run build
RUN npm run export

FROM nginx:1.19.2-alpine
COPY front/docker/app/nginx.conf /etc/nginx/   
COPY --from=builder /app/dist /var/www   
EXPOSE 80
STOPSIGNAL SIGQUIT
ENTRYPOINT ["nginx", "-g", "daemon off;"]