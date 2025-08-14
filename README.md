
# OpenAI Text Generation API on Kubernetes

  

This project demonstrates how to deploy a Flask-based API that uses OpenAI's GPT-4o mini model for text generation, running on a Kubernetes cluster in AWS EKS.

  

## Project Overview

  

This application provides a simple API endpoint that generates text responses based on user prompts using OpenAI's GPT-4o mini model. It's containerized using Docker and deployed on a Kubernetes cluster, making it scalable and easy to manage.

  

## Features

  

- Flask-based API server

- Integration with OpenAI's GPT-4o mini model

- Docker containerization

- Kubernetes deployment on AWS EKS

- GitHub Actions for CI/CD

  

## Prerequisites

  

- AWS account with EKS cluster set up

- Docker installed locally

- kubectl configured to interact with your EKS cluster

- OpenAI API key

  

## Project Structure

This repo's structure is as follows:

```
ai-powered-eks-project/
├── app.py
├── requirements.txt
├── Dockerfile
├── deploy.sh
├── dir.sh
├── openai-app-deployment.yaml
├── openai-app-service.yaml
├── openai-secrets.yaml
└── README.md
```

> **Note:** The Dockerfile expects `app.py` and `requirements.txt` to be in `app/src/`, but in this repo they are at the root. You must either:
> - Move `app.py` and `requirements.txt` into `app/src/`, or
> - Update the Dockerfile to use the correct paths (recommended: change `COPY app/src/requirements.txt .` to `COPY requirements.txt .` and `COPY app/src/app.py .` to `COPY app.py .`).

## Setup and Deployment

  

1. Clone the repository:

```bash
git clone https://github.com/yourusername/openai-eks-project1.git
```
```bash
cd openai-eks-project1
```
  
2. Build and push the Docker image:

  
```bash
docker build -t your-docker-repo/openai-app:latest .
```
```bash
docker push your-docker-repo/openai-app:latest
```
  
3. Create a Kubernetes secret for your OpenAI API key (must match the name in deployment YAML: `openai-secret`):

```bash
kubectl create secret generic openai-secret --from-literal=api-key=your_openai_api_key
```

4. Apply the Kubernetes configurations (YAMLs are at the root of this repo):
```bash
kubectl apply -f openai-app-deployment.yaml
kubectl apply -f openai-app-service.yaml
```

5. Get the LoadBalancer URL:
```bash
kubectl get services openai-app-service
```

## Usage

To generate text, send a POST request to the `/generate` endpoint:

```bash
curl -X POST http://your-loadbalancer-url/generate \
  -H "Content-Type: application/json" \
  -d '{"prompt": "What are the benefits of cloud computing?"}'
```

Response example:
```json
{"generated_text": "Cloud computing offers a wide range of benefits..."}
```

## CI/CD

This project uses GitHub Actions for continuous integration and deployment. The workflow is defined in `.github/workflows/deploy.yml`. It automatically builds and pushes the Docker image, and updates the Kubernetes deployment on push to the main branch.

## Monitoring and Maintenance

Monitor the application logs:
```bash
kubectl logs -f deployment/openai-app
```

Check pod status:
```bash
kubectl get pods
```

Update the deployment (e.g., after changing the Docker image):
```bash
kubectl set image deployment/openai-app openai-app=your-docker-repo/openai-app:new-tag
```

Troubleshooting Tips:

* Make sure the Docker image is built and pushed correctly.
* Verify that the Kubernetes secret is created with the correct name and API key.
* Check the YAML file locations and make sure they are applied correctly.
* If the deployment is not updating, try updating the image tag and re-applying the YAML files.