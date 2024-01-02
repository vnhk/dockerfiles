FROM node:20.9.0 as builder

WORKDIR /app

COPY ./pwpo-facade .

RUN rm -rf ./node_modules

RUN npm install

RUN npm run build:production

FROM nginx:1.15.8-alpine

COPY --from=builder /app/dist/pwpo-facade/ /usr/share/nginx/html
