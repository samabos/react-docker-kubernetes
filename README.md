# react-docker-kubernetes
Sample React app deployed as a docker container image to minikube for tutorial purposes

Deploying a React application in Docker to Kubernetes can be a multi-step process, especially for beginners. Here's a step-by-step tutorial that covers everything from setting up the development environment to deploying to a local Kubernetes cluster on Windows. We'll assume you're starting from scratch and provide instructions for each step.

**Prerequisites:**

1. **Windows OS**: Make sure you are using Windows as your operating system.

2. **Docker**: Install Docker for Windows. You can download it from [Docker's official website](https://docs.docker.com/get-docker/). Make sure Docker Desktop is running by running the docker desktop app or open cmd and run docker info.

3. **Kubernetes**: Install Minikube, which is a tool for running a single-node Kubernetes cluster. You can download it from the [Minikube GitHub repository](https://github.com/kubernetes/minikube/releases).

4. **kubectl**: Install `kubectl`, the Kubernetes command-line tool. You can use `choco` for package management on Windows or download it from the official Kubernetes release page (https://kubernetes.io/docs/tasks/tools/install-kubectl/).

5. **Node.js and npm**: Ensure you have Node.js and npm installed. You can download and install them from the [official website](https://nodejs.org/).

6. **React**: You'll need a React application to deploy. If you don't have one, create a simple React app using Create React App. Install it globally with `npm install -g create-react-app` and create an app with `npx create-react-app my-react-app`.

Now that you have the prerequisites, let's move on to deploying the React application to Kubernetes.

**Step 1: Build a Docker Image**

1. Open a terminal and navigate to your React app's root directory.

2. Create a Dockerfile in your project root. You can use a text editor or run the following command: `touch Dockerfile`.

3. Open the Dockerfile in your text editor and add the following content:

   ```Dockerfile
   # Use an official Node runtime as a parent image
   FROM node:14

   # Set the working directory in the container
   WORKDIR /app

   # Copy package.json and package-lock.json to the working directory
   COPY package*.json ./

   # Install app dependencies
   RUN npm install

   # Bundle app source
   COPY . .

   # Build your React app
   RUN npm run build

   # Expose a port to listen to the app
   EXPOSE 3000

   # Start the application
   CMD ["npm", "start"]
   ```

4. Save and close the Dockerfile.

5. Build the Docker image from the project's root directory using this command:

   ```
   docker build -t my-react-app .
   ```

**Step 2: Run the Docker Container Locally**

1. Once the Docker image is built successfully, you can run the container:

   ```
   docker run -p 3000:3000 my-react-app
   ```

   This command starts the React app in a Docker container and maps port 3000 on your laptop to port 3000 in the container.

2. Open your web browser and visit `http://localhost:3000` to see your React app running in the Docker container.

**Step 3: Setting up Kubernetes with Minikube**

1. Initialize Minikube by running the following command:

   ```
   minikube start
   ```

2. To confirm that Minikube is running, use the following command:

   ```
   kubectl cluster-info
   ```

   You should see the Kubernetes master and DNS endpoint information.

**Step 4: Deploy Your React App to Kubernetes**

1. Create a Kubernetes deployment YAML file, e.g., `react-app-deployment.yaml`. Open it in a text editor and add the following content:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: react-app-deployment
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: react-app
     template:
       metadata:
         labels:
           app: react-app
       spec:
         containers:
         - name: react-app-container
           image: my-react-app
           ports:
           - containerPort: 3000
   ```

2. Apply the deployment to your Minikube cluster:

   ```
   kubectl apply -f react-app-deployment.yaml
   ```

3. Expose your React app to an external IP using a Kubernetes Service. Create a `react-app-service.yaml` file with the following content:

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: react-app-service
   spec:
     selector:
       app: react-app
     ports:
     - protocol: TCP
       port: 80
       targetPort: 3000
     type: LoadBalancer
   ```

4. Apply the service to your Minikube cluster:

   ```
   kubectl apply -f react-app-service.yaml
   ```

5. Find the external IP address for your React app:

   ```
   minikube service react-app-service
   ```

   Your React app should be accessible at the provided IP address.

Congratulations! You've successfully deployed a React application in a Docker container to a local Kubernetes cluster on Windows. This tutorial covers all the necessary steps for a beginner to get started with this process.
