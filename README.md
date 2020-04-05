## Docker Getting Started

docker run -d -p 80:80 docker/getting-started

-d: detached (in background)

-p: port to expose host:container

or combine -dp

Basic Dockerfile:

FROM node:12-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "/app/src/index.js"]

Build command

docker build -t getting-started .

-t: tag name

. : Dockerfile root

docker images: you will see your image was created

Run command

docker run -dp 3000:3000 getting-started

docker ps: to see your container is up

tip: -a to see all containers

Remove Container

docker rm -f CONTAINER_NAME

-f: force to remove

Stop Container

docker stop CONTAINER_NAME

Create a volume

docker volume create todo-db

Create container with volume

docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started

Note: if you remove the container e create a new one with same volume

the data will be still there

Show logs

docker logs -f CONTAINER_NAME

Watch

-w / path

Example MYSQL container

docker run -d \ --network todo-app --network-alias mysql \ -v todo-mysql-data:/var/lib/mysql \ -e MYSQL_ROOT_PASSWORD=secret \ -e MYSQL_DATABASE=todos \ mysql:5.7

Acess database container

docker exec -it <mysql-container-id> mysql -p

Acess todo database MYSQL container

docker exec -ti <mysql-container-id> mysql -p todos

Migrate APP container and MYSQL container to docker-compose.yml

version: '3.7'

services:

app:

image: node:12-alpine

command: sh -c 'yarn install && yarn run dev'

ports:

- 3000:3000

working_dir: /app

volumes:

- ./:/app

environment:

MYSQL_HOST: mysql

MYSQL_USER: root

MYSQL_PASSWORD: secret

MYSQL_DB: todos

mysql:

image: mysql:5.7

volumes:

- todo-mysql-data:/var/lib/mysql

environment:

MYSQL_ROOT_PASSWORD: secret

MYSQL_DATABASE: todos

volumes:

todo-mysql-data:

Running your docker componse, on path docker-compose.yml

docker-compose up -d

To Stop

docker-compose stop

Logs

docker-compose logs -f

Specific

docker-compose logs -f app

Best Practices

FROM node:12-alpine
WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install --production
COPY . .
CMD ["node", "/app/src/index.js"]

Installing the dependencies and then copy

Examples:

Java Maven

FROM maven AS build
WORKDIR /app
COPY . .
RUN mvn package
FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/webapps

React

FROM node:12 AS build
WORKDIR /app
COPY package\* yarn.lock ./
RUN yarn install
COPY public ./public
COPY src ./src
RUN yarn run build
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
