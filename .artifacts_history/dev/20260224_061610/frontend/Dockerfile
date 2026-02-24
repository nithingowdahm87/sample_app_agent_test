# Stage 1: Builder
FROM node:latest AS builder
ARG GIT_SHA
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
RUN npm run build
# Stage 2: Runner
FROM node:latest
ARG GIT_SHA
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
RUN groupadd -r appuser -g 10001 && useradd -r -u 10001 -g appuser appuser
USER appuser
ENV NODE_ENV=production
ENV PORT=80
EXPOSE 80
HEALTHCHECK --interval=30s --timeout=5s --retries=3 CMD node -e "try { http.get('http://localhost:'+process.env.PORT, (r) => { if (r.statusCode === 200) process.exit(0); else process.exit(1); }); } catch (e) { process.exit(1); }"
CMD ["node", "dist/index.js"]
LABEL maintainer="Your Name"
LABEL version="1.0"
LABEL git_sha=$GIT_SHA