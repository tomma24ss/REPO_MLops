# Use an official Node.js LTS (Long Term Support) image as the base image
FROM node:18

# Create a directory for the application code inside the container
WORKDIR /app

# Copy only the package.json and yarn.lock files first
COPY package.json yarn.lock ./

# Install application dependencies
RUN yarn install --frozen-lockfile

# Now, copy the rest of the application source code to the container
COPY . .

# Expose port 3000 for the application
EXPOSE 3000

# Command to start the application when the container starts
CMD ["yarn", "start"]
