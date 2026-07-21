# Node.js backend API
# FROM node:18-alpine
# WORKDIR /app
# COPY package*.json ./
# RUN npm install
# COPY . .
# EXPOSE 3000
# CMD ["node", "server.js"]
FROM node:18-alpine
WORKDIR /app

# Copy all backend files into the container
COPY . .

# Expose backend port
EXPOSE 3000

# Run your server directly
CMD ["node", "server.js"]
