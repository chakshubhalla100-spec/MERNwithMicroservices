FROM node:20-alpine AS base
WORKDIR /app

# --- Hello Service Stage ---
FROM base AS hello
COPY helloService/package*.json ./
RUN npm install
COPY helloService/ .
EXPOSE 3001
CMD ["npm", "start"]


# --- Profile Service Stage ---
FROM base AS profile
COPY profileService/package*.json ./
RUN npm install
COPY profileService/ .
EXPOSE 3002
CMD ["npm", "start"]
