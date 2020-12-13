# Build image
FROM node:12-alpine as build

# Install curl and bash to add node-prune tool
RUN apk update && apk add curl bash && rm -rf /var/cache/apk/*
RUN curl -sfL https://install.goreleaser.com/github.com/tj/node-prune.sh | bash -s -- -b /usr/local/bin

WORKDIR /app

# Dependencies
COPY package*.json ./
RUN npm install

# Build
COPY . .
RUN npm run build

# Remove dev dependencies by npm
RUN npm prune --production

# Remove dev dependencies by node-prune tool
RUN /usr/local/bin/node-prune

# Production image
FROM node:12-alpine

WORKDIR /app

# Copy necessary filed from build image
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules

# Application
USER node
ENV PORT=8080
EXPOSE 8080

CMD ["node", "dist/main.js"]