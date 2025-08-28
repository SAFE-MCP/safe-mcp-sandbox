# Use a slim Debian base by default to reduce CVE findings versus Alpine.
# Easily override at build time: --build-arg NODE_BASE=node:22-alpine3.20
ARG NODE_BASE=node:22-bookworm-slim

FROM ${NODE_BASE} AS deps
WORKDIR /app
# Copy lockfile for reproducible installs and use npm ci
COPY server/package.json server/package-lock.json server/tsconfig.json ./
RUN npm ci --no-audit --no-fund
COPY server/src ./src
RUN npm run build \
  && npm prune --omit=dev

FROM ${NODE_BASE} AS runtime
ENV NODE_ENV=production
WORKDIR /app
# Official Node images include a non-root `node` user
COPY --from=deps /app/node_modules ./node_modules
COPY --from=deps /app/dist ./dist
COPY server/package.json ./package.json
USER node
ENTRYPOINT ["node", "dist/index.js"]
