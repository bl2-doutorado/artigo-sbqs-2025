# Stage image to build the application
FROM node:21.1.0-alpine as build
WORKDIR /app
COPY ./package.json ./yarn.lock ./
RUN yarn install

# Final image to start the application
FROM node:21.1.0-alpine as server

LABEL organization="My Organization"
LABEL team="FinOps"
LABEL version="1.0.0"
LABEL description="Builds the image of the application"

WORKDIR /app
COPY . .
COPY --from=build /app/node_modules/ ./node_modules/
RUN yarn build

# Exposes application port
EXPOSE 3000
CMD ["yarn", "start"]