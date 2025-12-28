# ────────────────────────────────
# 1. Development stage (used only locally)
# ────────────────────────────────
FROM node:lts-alpine AS development

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install ALL dependencies (including devDependencies for Vite HMR)
RUN npm install

# Copy source code
COPY . .

# Expose Vite dev server port (used only in local dev)
EXPOSE 8080

# Hot reload dev server
CMD ["npm", "run", "dev:mobile"]


# ────────────────────────────────
# 2. Production build stage
# ────────────────────────────────
FROM node:lts-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build


# ────────────────────────────────
# 3. Final production image – super small + serves on port 80
# ────────────────────────────────
FROM nginx:alpine AS production

# Copy built assets from builder stage
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy custom nginx config (optional but recommended)
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 80 (standard HTTP)
EXPOSE 80

# Nginx runs automatically
CMD ["nginx", "-g", "daemon off;"]