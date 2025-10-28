#-------build stage-----------#
FROM node:22.9.0-alpine@sha256:c9bb43423a6229aeddf3d16ae6aaa0ff71a0b2951ce18ec8fedb6f5d766cf286 AS builder

WORKDIR /app
COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

#------final stage------------#
FROM nginx:stable-alpine3.21-perl@sha256:71584d41516107f8cfcf5bc2280f0886f2cf821bd2b42d5cf56e96b02bcdb92a

EXPOSE 80
EXPOSE 443

COPY --from=builder /app/build /usr/share/nginx/html 
COPY ./nginx-configs/site/default.conf /etc/nginx/conf.d/default.conf

CMD ["nginx", "-g", "daemon off;"]