# Stage 1: Build the Next.js app
FROM node:18-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./
COPY turbo.json ./
COPY tsconfig.json ./
COPY packages ./packages
COPY apps/user-app ./apps/user-app

RUN npm install
RUN npm run turbo run build --filter=user-app...

# Stage 2: Setup the production environment for Next.js
FROM node:18-alpine AS nextjs

WORKDIR /app

COPY --from=builder /app/apps/user-app/.next ./.next
COPY --from=builder /app/apps/user-app/public ./public
COPY apps/user-app/package.json ./

RUN npm install --only=production

EXPOSE 3000

CMD ["npm", "run", "dev"]

# Stage 3: Setup the production environment for Express.js
FROM node:18-alpine AS express

WORKDIR /app

COPY apps/express-app/package.json ./
RUN npm install --only=production

COPY apps/bank-webhook ./

EXPOSE 3003

CMD ["node", "dist/index.js"]
