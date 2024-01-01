FROM node:18.16.0 as builder

WORKDIR /app

COPY pwpo-facade/package.json pwpo-facade/package-lock.json ./

RUN npm install

COPY ./pwpo-facade .

RUN npm run build --configuration=production

FROM nginx:1.15.8-alpine

COPY --from=builder /app/dist/pwpo-facade/ /usr/share/nginx/html
