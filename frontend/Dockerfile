# Build stage - use full Node.js image for building
FROM node:22-alpine AS builder

WORKDIR /app
COPY package*.json ./
# Smart npm install - use ci if lock file exists, install otherwise
RUN if [ -f "package-lock.json" ]; then \
        npm ci && npm cache clean --force; \
    else \
        npm install && npm cache clean --force; \
    fi

# Production stage - Debian slim with aggressive Go removal
FROM node:22-slim

WORKDIR /app

# Aggressively remove all Go-related packages and binaries to eliminate CVEs
RUN apt-get update && \
    # Remove ALL Go packages and binaries completely
    apt-get remove -y --purge \
        golang* \
        go-* \
        gccgo* \
        '*golang*' \
        '*-go' \
        '*-go-*' 2>/dev/null || true && \
    # Search and remove any remaining Go binaries
    find /usr -name "*go*" -type f -executable -delete 2>/dev/null || true && \
    find /bin -name "*go*" -type f -delete 2>/dev/null || true && \
    find /sbin -name "*go*" -type f -delete 2>/dev/null || true && \
    # Install only essential packages
    apt-get install -y --no-install-recommends \
        dumb-init \
        ca-certificates && \
    # Final cleanup
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* /usr/share/man/* /usr/share/info/*

# Copy built dependencies from builder
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
COPY . .

# Create non-root user for security
RUN groupadd -g 1001 nodejs && \
    useradd -r -u 1001 -g nodejs nodejs && \
    chown -R nodejs:nodejs /app

USER nodejs

EXPOSE 5173

# Health check for development server
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD node -e "require('http').get('http://localhost:5173/', (res) => { process.exit(res.statusCode === 200 ? 0 : 1) })"

# Use dumb-init to handle signals properly
ENTRYPOINT ["dumb-init", "--"]
CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]