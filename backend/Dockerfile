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

# Copy built node_modules from builder with correct ownership
COPY --from=builder --chown=65532:65532 /app/node_modules ./node_modules

# Copy application files with correct ownership
COPY --chown=65532:65532 package*.json ./
COPY --chown=65532:65532 . .

# Explicitly set user (nonroot = UID 65532)
USER 65532

EXPOSE 3000

# Distroless doesn't support HEALTHCHECK or ENTRYPOINT with init
# Direct Node.js execution for maximum security
CMD ["server.js"]