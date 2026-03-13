# Node image
FROM node:22-alpine

# Set the working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy your backend code (which now includes the static/build folder)
COPY . .

# Expose the port the backend runs on
EXPOSE 5000

# Start the server
CMD ["node", "server.js"]