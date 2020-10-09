# Task 2: 
# Given Dockerfile was very inefficient.  Converted it to a multi-stage docker build with a node12-alpine container used as runtime.
# Resulting image is of size 270 MB.
FROM node:12-alpine as builder
WORKDIR /app
COPY ./package.json ./
RUN npm cache clean --force && npm cache verify && npm install
COPY . .
RUN npm run build

FROM node:12-alpine
WORKDIR /prod/app
COPY --from=builder ./app ./
EXPOSE 8080
CMD ["npm", "run", "start"]