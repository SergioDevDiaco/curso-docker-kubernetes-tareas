# Stage 1: Build
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
# Stage 2: Production
FROM node:18-alpine
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001
WORKDIR /app
COPY package*.json ./
RUN npm i --only=production && \
    npm cache clean --force
COPY --from=build /app/app.js ./
RUN chown -R nodejs:nodejs /app
USER nodejs
EXPOSE 3000
ENV NODE_ENV=production \
    PORT=3000

    
LABEL maintainer="sergio.salcedo@diaconia.bo"
LABEL version="1.0"
LABEL description="Aplicación Node.js con Docker multi-stage"

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {process.exit(r.statusCode === 200 ? 0 : 1)})"
CMD ["node", "app.js"]
