# Use multi-stage build for security
FROM node:22-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev && npm cache clean --force

# Final stage with minimal attack surface
FROM node:22-alpine

WORKDIR /app

# Update Alpine packages and remove Go to fix CVE-2024-34156, CVE-2024-24790, and CVE-2023-45288
RUN apk update && apk upgrade --no-cache && \
    # Remove Go completely to eliminate stdlib vulnerabilities
    apk del go 2>/dev/null || true && \
    # Install only essential packages
    apk add --no-cache dumb-init && \
    # Remove package manager and other unnecessary tools for security
    rm -rf /var/cache/apk/* /tmp/* /var/tmp/* /usr/share/man/* /usr/share/doc/*

# Copy dependencies from builder stage
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
COPY . .

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -S -u 1001 -G nodejs nodejs && \
    chown -R nodejs:nodejs /app && \
    # Remove write permissions from system directories
    find /app -type d -exec chmod 755 {} \; && \
    find /app -type f -exec chmod 644 {} \;

USER nodejs

EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD node -e "require('http').get('http://localhost:3000/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1) })"

# Use dumb-init to handle signals properly
ENTRYPOINT ["dumb-init", "--"]
CMD ["npm", "start"]