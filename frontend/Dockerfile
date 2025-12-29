# ──────────────────────
# 1. Development stage (local hot reload)
# ──────────────────────
FROM node:lts-alpine AS development
WORKDIR /app
COPY package*.json ./
RUN npm install                     # ← ALL deps (including vue-cli-service)
COPY . .
EXPOSE 8080
CMD ["npm", "run", "dev:mobile"]


# ──────────────────────
# 2. Build stage (for production)
# ──────────────────────
FROM node:lts-alpine AS builder
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install ALL dependencies (including vue-cli-service for building)
RUN npm ci                          # ← NOT --only=production!

# Copy source code
COPY . .

# Build the app
RUN npm run build                   # ← now works because vue-cli-service exists


# ──────────────────────
# 3. Production stage (tiny Nginx image)
# ──────────────────────
FROM nginx:alpine AS production
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]