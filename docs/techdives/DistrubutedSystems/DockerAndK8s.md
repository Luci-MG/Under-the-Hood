# **Docker and Kubernetes**

## **Overview**

### **Docker**
Docker is a platform that enables developers to build, package, and run applications in lightweight containers. It ensures applications are portable and can run consistently across different environments, from development to production.  
   
!!! note "Key Components"
    - Containers:  Encapsulate applications with all their dependencies, ensuring consistency across systems. Containers share the host OS kernel, making them more lightweight than traditional virtual machines.
    - Images:  Immutable templates used to create containers, typically defined in a `Dockerfile`. Images are layered and reusable.
    - Docker Compose:  A tool to define and manage multi-container Docker applications via a single YAML file, simplifying orchestration on a single machine.

### **Kubernetes**
Kubernetes (often abbreviated as K8s) is an open-source platform for automating the deployment, scaling, and management of containerized applications. It abstracts away the complexity of running containers at scale across multiple machines.

!!! note "Key Components"
    - Pods:  The smallest deployable units in Kubernetes. A Pod can host one or more tightly coupled containers.
    - Services:  Define stable network endpoints for Pods, enabling communication between components in the cluster.
    - Cluster:  A group of nodes managed by a control plane that runs applications as Pods across multiple machines for load balancing and reliability.

---

## **Hierarchy and Relationship**

### **Docker vs Kubernetes**
Docker focuses on building, packaging, and running containers. It handles application-level concerns whereas Kubernetes focuses on orchestrating, scaling, and managing containers across distributed environments.

### **How they Work Together**
- Container Runtime: Kubernetes uses Docker as one of its container runtimes, though other options like containerd or CRI-O are available.
- Complementary Roles: Docker handles container creation, while Kubernetes ensures containers are deployed and managed across the entire cluster efficiently.


## Using Docker with Kubernetes

???+ info "Steps" 
      Create a Dockerfile for your application and build the image
      ```bash title="Step-1: Build Docker Images"
      docker build -t my-app:latest .
      ```

      Store the image in a registry (e.g., Docker Hub)
      ```bash title="Step-2: Push to Registry"
      docker push my-app:latest
      ```

      Use the built Docker image in a Kubernetes Deployment YAML file and apply it with:
      ```bash title="Deploy on Kubernetes"
      kubectl apply -f deployment.yaml
      ```


## 4. Creating and Managing Docker and Kubernetes Components Individually

### Creating Docker Components

1. Install Docker:  
   Follow [Docker’s official installation guide](https://docs.docker.com/get-docker/) for your operating system.

2. Create a Dockerfile:  
   Example:
   ```Dockerfile
   FROM python:3.9
   WORKDIR /app
   COPY . .
   RUN pip install -r requirements.txt
   CMD ["python", "app.py"]
   ```

3. Build and Run Docker Containers:
   - Build Image:
     ```bash
     docker build -t my-app:latest .
     ```
   - Run Container:
     ```bash
     docker run -d -p 5000:5000 my-app:latest
     ```

4. Use Docker Compose:
   - Define services in a `docker-compose.yml`:
     ```yaml
     version: '3.8'
     services:
       web:
         image: my-app:latest
         ports:
           - "5000:5000"
     ```
   - Start services with:
     ```bash
     docker-compose up
     ```

---

### Creating Kubernetes Components

1. Install Kubernetes:  
   Use Minikube for local development or create a production cluster using cloud providers like GKE, AKS, or EKS.

2. Define Kubernetes Resources:  
   Create YAML manifests for Deployments and Services.

   - Deployment YAML:
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: my-app
     spec:
       replicas: 2
       selector:
         matchLabels:
           app: my-app
       template:
         metadata:
           labels:
             app: my-app
         spec:
           containers:
             - name: my-app
               image: my-app:latest
               ports:
                 - containerPort: 5000
     ```

   - Service YAML:
     ```yaml
     apiVersion: v1
     kind: Service
     metadata:
       name: my-app-service
     spec:
       type: NodePort
       ports:
         - port: 5000
           targetPort: 5000
       selector:
         app: my-app
     ```

3. Deploy to Kubernetes:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

---

## 5. Deployment Scenarios

### Single Machine Deployment

- With Docker Compose:  
  Define services in `docker-compose.yml` and run:
  ```bash
  docker-compose up
  ```

- With Kubernetes (Using Minikube):  
  - Install Minikube and create a cluster.
  - Apply your Kubernetes manifests with `kubectl`.

### Multiple Machines Deployment

- Using Docker Swarm:  
  - Initialize the Swarm cluster:  
    ```bash
    docker swarm init
    ```
  - Use overlay networks for service communication across nodes.

- Using Kubernetes:  
  - Set up a Kubernetes cluster using cloud providers.
  - Use Kubernetes services to enable seamless inter-pod communication.

---

## 6. Communication Between Services

- Single Machine Communication:
  - Docker Compose: Services communicate using their service names as hostnames.
  - Kubernetes: Pods use DNS names or Service endpoints to communicate.

- Multiple Machines Communication:
  - Docker Swarm: Overlay networks allow services on different nodes to interact.
  - Kubernetes: The flat networking model enables Pods to communicate across nodes transparently.

---

## 7. Key Differences Between Docker Compose and Kubernetes

| Aspect            | Docker Compose                    | Kubernetes                          |
|-----------------------|----------------------------------------|-----------------------------------------|
| Purpose           | Local development and testing         | Production orchestration               |
| Configuration     | Single `docker-compose.yml` file      | Multiple YAML files for resources      |
| Scaling           | Manual                                | Automated scaling with `kubectl scale` |
| High Availability | Limited                               | Built-in redundancy and self-healing   |
| Use Case          | Simple applications on one machine    | Complex workloads across clusters      |

---

## 8. Additional Considerations

- Persistent Storage:
  - Docker: Use Docker volumes to store persistent data.
  - Kubernetes: Use Persistent Volumes (PV) and Persistent Volume Claims (PVC).

- Monitoring and Logging:
  - Use Prometheus and Grafana in Kubernetes for comprehensive monitoring.

- CI/CD Integration:
  - Integrate Docker and Kubernetes into pipelines using tools like Jenkins or GitLab CI for automated builds and deployments.

---

## 9. How to Use Docker Compose to Get the Application from GitHub and Build the Docker Image

---

### Getting from Github and using Docker Compose Build Context

Docker Compose can also use the build context to pull code directly from GitHub when creating an image. Here’s how:

1. Create a `docker-compose.yml` with GitHub Repository as the Build Context:

   ```yaml
   version: '3.8'
   services:
     app:
       build:
         context: https://github.com/your-username/your-repo.git
         dockerfile: Dockerfile
       ports:
         - "5000:5000"
   ```

2. Run Docker Compose to Build and Start the Container:

   ```bash
   docker-compose up --build
   ```

Note:  
- This method works only if the GitHub repository is public. For private repositories, you’ll need to provide authentication (e.g., via SSH keys or GitHub tokens).
- Docker will pull the latest version from the specified GitHub repository and build the image based on the `Dockerfile` in the repository.

---

## 10. Silly and Practical Questions (Numbered)

1. Can I use a Docker Compose file with Kubernetes?  
   Not directly. Kubernetes doesn’t understand Docker Compose syntax, but there are tools like Kompose that can convert Docker Compose files into Kubernetes YAML files.

2. What happens if I try to run a Docker Compose file inside a Kubernetes cluster?  
   It won’t work. Kubernetes will look at you confused (figuratively), because it expects YAML manifests with its own syntax, not a `docker-compose.yml`.

3. Why do Kubernetes YAML files look scarier than Docker Compose files?  
   Kubernetes YAML files are more complex because they handle more advanced scenarios like scaling, networking, and rolling updates, which Docker Compose doesn’t attempt to address.

4. Do I need to uninstall Docker if I switch to Kubernetes?  
   Nope! Docker is still useful for building and testing images locally, even if you’re deploying to Kubernetes. In fact, Kubernetes can use Docker as a container runtime.

5. Will Docker containers fight with Kubernetes Pods if they run on the same machine?  
   Nope, they’ll coexist peacefully. Docker containers and Kubernetes Pods can run side by side without conflict. They’re friends, not rivals!

6. Can I copy-paste my Docker Compose file into Kubernetes and hope it works?  
   Sorry, no shortcuts here. You need to convert the Compose file into Kubernetes resources, either manually or using tools like Kompose.

7. Is Docker Compose faster than Kubernetes because it has fewer YAML files?  
   Yes, Docker Compose is faster to set up for local development because it’s simpler. But for production-scale orchestration, Kubernetes is much more powerful.

8. How do I know if my container is happy inside a Kubernetes Pod?  
   Check with this command:
   ```bash
   kubectl get pods
   ```
   If the status is `Running`, your container is content. If you see `CrashLoopBackOff`, it’s definitely not happy!

9. Can I use Kubernetes without the cloud, or will it complain?  
   You can use Minikube or kind (Kubernetes in Docker) to run Kubernetes locally on your machine. No cloud required.

10. What’s the difference between `docker-compose up` and `kubectl apply -f`?  
    - `docker-compose up`: Starts containers defined in a `docker-compose.yml` file.  
    - `kubectl apply -f`: Deploys resources (like Pods, Deployments) described in a Kubernetes YAML file to your cluster.

11. Do I still need to learn Docker Swarm if I already know Kubernetes?  
    Not really. Docker Swarm is simpler but not as widely used in production as Kubernetes. Kubernetes has become the de facto standard.

12. Can a single Pod run multiple Docker Compose services?  
    Yes! A Pod can run multiple containers, similar to how Docker Compose runs multiple services. However, in Kubernetes, these containers should be tightly coupled (e.g., sharing resources).

13. If Docker Compose is easier, why do people torture themselves with Kubernetes?  
    Kubernetes offers features like scaling, self-healing, and load balancing. It’s overkill for simple projects but essential for large, distributed applications.

14. Is Kubernetes just a fancy way of saying, “I don’t want to use Docker Compose”?  
    Not exactly. Docker Compose is great for local setups, while Kubernetes is a powerful orchestration tool for running applications across multiple nodes at scale.

15. What’s the difference between a Pod and a Container? Can I use the words interchangeably?  
    Not quite. A Pod is a wrapper that can contain one or more containers. Pods are the smallest deployable unit in Kubernetes, but a container is just an isolated environment for running applications.

16. If a container crashes in Kubernetes, does Kubernetes get sad?  
    Nope! Kubernetes will restart the container automatically. That’s part of its self-healing magic.

17. Will my application break if I use a Docker image from 2015?  
    It might! Older images could have compatibility issues or security vulnerabilities. Use them only if you’re sure they still meet your needs.

18. Is Kubernetes allergic to Windows, or will it run happily there?  
    Kubernetes supports Windows nodes, but the experience is smoother with Linux. Most people deploy Kubernetes on Linux-based clusters.

19. Can I use both Docker and Kubernetes at the same time? Or will it cause chaos?  
    Yes, you can use both. Build your containers with Docker, push them to a registry, and deploy them with Kubernetes. No chaos – just smooth workflows.

20. Why can’t Docker Compose just learn scaling and take over Kubernetes' job?  
    Docker Compose is intentionally lightweight and simple. Adding Kubernetes-like features would complicate it and defeat its original purpose.

21. How much YAML is too much YAML?  
    If you start dreaming in YAML, it’s probably too much. But seriously, Kubernetes relies heavily on YAML, so learning to manage it effectively is key.

22. Can Kubernetes work without YAML files? (Please say yes!)  
    Unfortunately, no. YAML files are essential for defining resources in Kubernetes. You can use Helm charts to simplify it, but YAML is unavoidable.

23. What happens if I forget to push my Docker image before deploying with Kubernetes?  
    Your deployment will fail because Kubernetes won’t find the image in the registry. Always remember to push!

24. Can I use `kubectl` commands on Docker containers?  
    Nope. `kubectl` is specifically for managing Kubernetes resources. Use `docker` commands for Docker containers.

25. Is Kubernetes only for tech wizards, or can normal humans use it too?  
    Normal humans can use it too! The learning curve is steep, but with practice, anyone can master it.

26. Do I need to sacrifice sleep to understand Kubernetes?  
    Maybe at first. But once you get the hang of it, Kubernetes will become your friend, and sleep will return.

27. Can a Docker container tell the difference between running on Kubernetes and Docker Compose?  
    Nope! The container itself doesn’t care where it’s running. As long as it gets its dependencies and configuration, it’ll happily run anywhere.

28. Can I run two Docker Compose files on one machine?  
   Yes, use the `-p` option to specify different project names for each Compose file.

29. Can services communicate across multiple machines?  
   Yes, with Docker Swarm or Kubernetes, services can communicate across machines using overlay networks or Kubernetes networking.

30. Is Docker Compose suitable for production?  
   Not recommended for large-scale production. Use Kubernetes or Docker Swarm instead.

31. How do I set up Kubernetes on a single machine?  
   Use Minikube to run a local Kubernetes cluster.

32. What file formats are used by Docker and Kubernetes?  
   Docker uses `Dockerfile` and `docker-compose.yml`. Kubernetes uses YAML files for resources like Deployments and Services.

---