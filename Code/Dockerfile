FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

# Install production dependencies only
RUN npm ci --omit=dev

COPY . .

# Set environment to production
ENV NODE_ENV=production

CMD ["node", "server.js"]