# Build stage - use full Node.js image for building
FROM node:22-alpine AS builder

WORKDIR /app
COPY package*.json ./
# Smart npm install - use ci if lock file exists, install otherwise
RUN if [ -f "package-lock.json" ]; then \
        npm ci --omit=dev && npm cache clean --force; \
    else \
        npm install --omit=dev && npm cache clean --force; \
    fi

# Production stage - Google Distroless (NO Go, NO shell, NO package manager)
FROM gcr.io/distroless/nodejs22-debian12:nonroot

# Set working directory
WORKDIR /app

# Copy built node_modules from builder
COPY --from=builder /app/node_modules ./node_modules

# Copy application files
COPY package*.json ./
COPY . .

# Distroless runs as nonroot user (UID 65532) by default - maximum security
# No shell, no package manager, no Go binaries

EXPOSE 3000

# Distroless doesn't support HEALTHCHECK or ENTRYPOINT with init
# Direct Node.js execution for maximum security
CMD ["server.js"]