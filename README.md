# Microservices Architecture using Node.js, Docker, and EC2

This project demonstrates a basic microservices architecture built using Node.js. It includes four independent services:

- **User Service** (`port 3000`) – Provides user data
- **Product Service** (`port 3001`) – Provides product data
- **Order Service** (`port 3002`) – Handles order creation
- **Gateway Service** (`port 3003`) – Aggregates all services into a unified API

These services are Dockerized, deployed using Docker Compose, and intended for deployment on an Amazon EC2 instance.

---

---

## EC2 User Data Script

Use this script when launching your EC2 instance to automate the installation of Docker, Docker Compose, and cloning of your repository.

```bash
#!/bin/bash
# Update packages
sudo apt update -y && sudo apt upgrade -y

# Install Docker
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

# Install Docker Compose (v2)
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Add Ubuntu user to Docker group
usermod -aG docker ubuntu

# Install Git
sudo apt install -y git

# Clone your repo
cd /home/ubuntu
git clone https://github.com/JOYSTON-LEWIS/Microservices-Task-Test.git
cd Microservices-Task-Test

# Build and run services
sudo docker-compose build
sudo docker-compose up -d
```

# Dockerfile (Common for All Services)
Create a Dockerfile in this manner: 
Microservices -> gateway-service -> Dockerfile
Microservices -> order-service -> Dockerfile
Microservices -> product-service -> Dockerfile
Microservices -> user-service -> Dockerfile

Each service has a similar Dockerfile based on Alpine for lightweight Node.js images:

```bash
# Dockerfile (Node Alpine)
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

# Docker Compose Configuration
Place this file at the root of the repository:
Microservices-Task-Test -> docker-compose.yaml

```yaml
version: '3.8'

services:
  user-service:
    build: ./Microservices/user-service
    container_name: user-service
    ports:
      - "3000:3000"

  product-service:
    build: ./Microservices/product-service
    container_name: product-service
    ports:
      - "3001:3001"

  order-service:
    build: ./Microservices/order-service
    container_name: order-service
    ports:
      - "3002:3002"

  gateway-service:
    build: ./Microservices/gateway-service
    container_name: gateway-service
    ports:
      - "3003:3003"
    depends_on:
      - user-service
      - product-service
      - order-service

```

# Build and Deploy (On EC2)

```bash
# Navigate to project directory
cd ~/Microservices-Task-Test

# Build all Docker images
sudo docker-compose build

# Run all containers
sudo docker-compose up -d

# Check running containers
sudo docker ps
```

# Validate Services
Access these URL in browser or use curl

```bash
curl http://<EC2_PUBLIC_IP>:3000/health       # User Service
curl http://<EC2_PUBLIC_IP>:3001/health       # Product Service
curl http://<EC2_PUBLIC_IP>:3002/health       # Order Service
curl http://<EC2_PUBLIC_IP>:3003/health       # Gateway Service
```

```bash
curl http://<EC2_PUBLIC_IP>:3003/api/users
curl http://<EC2_PUBLIC_IP>:3003/api/products
curl http://<EC2_PUBLIC_IP>:3003/api/orders
```

# Screenshots

![Screenshot 2025-05-10 155215](https://github.com/user-attachments/assets/490e8ed4-5435-4ea3-b438-ce76ad8b4603)

![Screenshot 2025-05-10 160412](https://github.com/user-attachments/assets/f124e2dc-a239-4ccb-9b5c-56d4e09a16a7)

![Screenshot 2025-05-10 160434](https://github.com/user-attachments/assets/27ad5040-a1e3-4216-b431-168d11830fd1)

![Screenshot 2025-05-10 160443](https://github.com/user-attachments/assets/236b9b5e-b49d-49cc-8a10-7a57f02e21fb)

![Screenshot 2025-05-10 160451](https://github.com/user-attachments/assets/aea368df-733d-42a0-bb6a-dff61a018327)

![Screenshot 2025-05-10 160458](https://github.com/user-attachments/assets/5c658210-2068-4836-906f-d533609de7f8)

![Screenshot 2025-05-10 160506](https://github.com/user-attachments/assets/588f252c-e22f-4e5d-a74e-61cb58373331)

![Screenshot 2025-05-10 160515](https://github.com/user-attachments/assets/adb0a801-72b9-472f-95b8-5d356c9d0938)

![Screenshot 2025![Screenshot 2025-05-10 160529](https://github.com/user-attachments/assets/c1f16af4-fd27-4701-b7fd-2840e1807678)

![Screenshot 2025-05-10 160529](https://github.com/user-attachments/assets/58e408d0-4410-4f5c-8906-abe1b5c52f67)

# END OF PROJECT

