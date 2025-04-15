# Zay Shop App CI/CD Pipeline Project

## INTRODUCTION

This presentation outlines the development and implementation of a Continuous Integration and Continuous Deployment (CI/CD) pipeline for automating the deployment of a website from GitHub to a Kubernetes cluster. The pipeline leverages a variety of tools including Jenkins, Ansible, Docker, and Kubernetes to create a streamlined, efficient process that ensures the web application is continuously integrated, deployed, and updated based on changes to the source code repository.

## Tools and Technologies Used

- **Jenkins**: Automation server used for building the CI/CD pipeline.
- **Ansible**: Configuration management tool used to automate deployment processes.
- **Kubernetes**: Container orchestration system for automating application deployment.
- **Docker**: Containerization platform for deploying applications in lightweight containers.
- **GitHub**: Source control platform used to store the application's code.
- **Git**: Source version control system.
- **Minikube**: Local Kubernetes cluster for development and testing.

## Project Structure

```
├── Ansible/              # Ansible playbooks for automation
│   ├── create-image-cafe-app.yml  # Builds Docker image
│   ├── inventory         # Ansible inventory file
│   └── k8s-deploy.yml    # Deploys app to Kubernetes
├── K8S/                  # Kubernetes manifests
│   ├── cafe-app-deployment.yml    # Pod deployment config
│   └── cafe-app-svc.yml  # Service configuration
├── assets/               # Web app static assets
├── Dockerfile            # Docker image definition
├── Jenkinsfile           # Jenkins pipeline definition
├── docker-compose.yml    # Local Docker deployment config
└── [HTML files]          # Web application source
```

## Pipeline Workflow

1. **Code Changes**: Developer pushes changes to GitHub repository
2. **Jenkins Trigger**: Webhook triggers Jenkins pipeline
3. **Checkout**: Jenkins pulls the latest code
4. **Transfer**: Code is transferred to Ansible server
5. **Build**: Ansible builds Docker image
6. **Test**: Basic tests are executed
7. **Verify Kubernetes**: Pipeline checks Kubernetes cluster connectivity
8. **Deploy**: Application is deployed to Kubernetes with:
   - Pods running the containerized application
   - Service exposing the application
9. **Expose Service**: Application is made accessible via public IP

## Setup Instructions

### Prerequisites

- Jenkins server with necessary plugins
- Ansible server
- Kubernetes cluster (Minikube)
- Docker installed on build server
- Git repository access

### Jenkins Setup

1. Install required plugins:
   - Git Integration
   - SSH Agent
   - Pipeline

2. Configure Jenkins credentials:
   - Add SSH credentials for Ansible server
   - Add GitHub webhook

### Deployment Configuration

1. Ensure Minikube is running:
   ```bash
   minikube start
   ```

2. Access the application:
   - The application is exposed on port 8080
   - Access via: http://[server-public-ip]:8080

## Troubleshooting

### Common Issues

1. **ImagePullBackOff Error**:
   - Ensure Docker image is built correctly
   - Check image is loaded into Minikube with: `minikube image load depi-shop:latest`

2. **Service Not Accessible**:
   - Verify port forwarding is active
   - Check security group settings if using cloud provider
   - Ensure the service is running with: `kubectl get svc`

3. **Pipeline Failures**:
   - Check Jenkins logs
   - Verify SSH connectivity to Ansible server

## Design

![CI/CD Pipeline Architecture](https://private-user-images.githubusercontent.com/105943548/378933603-49de5482-d67d-4fe2-b988-49a466578475.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mjk2MTU0NzcsIm5iZiI6MTcyOTYxNTE3NywicGF0aCI6Ii8xMDU5NDM1NDgvMzc4OTMzNjAzLTQ5ZGU1NDgyLWQ2N2QtNGZlMi1iOTg4LTQ5YTQ2NjU3ODQ3NS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQxMDIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MTAyMlQxNjM5MzdaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zYTk2MTY2NzQzMmJiYmQ2ZjVkMzFlMWMzNjc2MDQzZTIzZTliYzJkNjZmYjk0M2Y1YzFiMmEwZjc0ZGVkNjYzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.UZ8NJ_6th41dc6PG5_ZZnhzIarLZfj5wTRk6mrmyvB4)

## Demo

![Application Screenshot](https://private-user-images.githubusercontent.com/105943548/378932858-bd5fc291-3e8c-4592-81f5-f1ae25c9c677.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3Mjk2MTU0NzcsIm5iZiI6MTcyOTYxNTE3NywicGF0aCI6Ii8xMDU5NDM1NDgvMzc4OTMyODU4LWJkNWZjMjkxLTNlOGMtNDU5Mi04MWY1LWYxYWUyNWM5YzY3Ny5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQxMDIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MTAyMlQxNjM5MzdaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zN2FmNmM2ZDE1YTIxNmRjM2YyN2Q0NWU2MjI5YzUwMTQ5MzdkM2RmOTUzY2I3ZmRhZDBlMGFiNDkyODgzMDdmJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.HmfmXoaZavYyv7lw3s1taNcJAQz_u15zfQOAhASjkBA)

## Future Enhancements

1. **Auto-scaling Configuration**: Implement Kubernetes Horizontal Pod Autoscaler
2. **Monitoring Integration**: Add Prometheus and Grafana for metrics
3. **Blue/Green Deployment**: Implement zero-downtime deployment strategy
4. **SSL/TLS Configuration**: Secure the application with HTTPS
5. **Multi-environment Support**: Add staging and production environments

## Contributors

- Eslam Sadawi

## License

This project is licensed under the MIT License - see the LICENSE file for details.


