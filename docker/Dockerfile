# Step 1: Define the base image
FROM node:18-alpine

# Step 2: Set the working directory in the container
WORKDIR /usr/src/app

# Step 3: Copy package.json and package-lock.json to the container
# This ensures that dependencies are installed first (and avoids re-installing every time)
COPY package*.json ./

# Step 4: Install dependencies inside the container
RUN npm install --production

# Step 5: Copy the application source code to the container
COPY . .

# Step 6: Expose the port the app will run on (e.g., 3000)
EXPOSE 3000

# Step 7: Define the command to run the application
CMD ["npm", "start"]