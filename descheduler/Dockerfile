dockerfileCopy code# 第一个构建阶段，用于构建应用程序
FROM node:14 AS build
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build

# 第二个构建阶段，用于运行应用程序
FROM node:14-alpine
WORKDIR /app
COPY --from=build /app/build ./build
RUN npm install -g serve
CMD ["serve", "-s", "build"]
