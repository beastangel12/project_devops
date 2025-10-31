# Step 1: Build stage
FROM node:18-alpine AS builder
WORKDIR /app

# copy package files first for caching
COPY package*.json ./

# install deps
RUN npm ci --silent

# copy next-i18next config explicitly (important)
COPY next-i18next.config.js ./

# copy rest of source
COPY . .

# build
RUN npm run build

# Step 2: Runner stage
FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

# copy build artifacts and necessary files
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/node_modules ./node_modules

# also copy the runtime i18n config
COPY --from=builder /app/next-i18next.config.js ./next-i18next.config.js

EXPOSE 3000
CMD ["npm","run","start"]
