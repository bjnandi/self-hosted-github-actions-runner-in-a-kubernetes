# Self Hosted GitHub Actions Runner in Kubernetes

Step 1: Project Structure Setup
- Clone the repository:
```
git clone https://github.com/bjnandi/self-hosted-github-actions-runner-in-kubernetes
```
- Setup githubs default account:
```
git config user.email "<your-email>"
git config user.name "<Your Name>"
```

Step 2: Creating the Custom runner image
- Create Directory for github-runner:
```
cd github-runner-k8s
```
Step 3: Entrypoint Script
- Make the script executable:
```
chmod +x entrypoint.sh
```
Step 4: Build the docker image
- Docker Build
```
docker build \
  --build-arg RUNNER_VERSION="2.302.1" \
  --build-arg GITHUB_PERSONAL_TOKEN="<your-personal-access-token" \
  --build-arg GITHUB_OWNER="<your-github-username>" \
  --build-arg GITHUB_REPOSITORY="<your-github-repository-name" \
  -t runner-image .
```
- Docker Login
```
docker login
```
- Tag the Docker Image
```
docker tag runner-image:latest bjnandi/self-hosted-github-actions-runner-in-a-kubernetes:latest
```
- Push the Image to DockerHub
```
docker push bjnandi/self-hosted-github-actions-runner-in-a-kubernetes:latest
```
Step 5: Push the Image to DockerHub
Step 6: Configure the self hosted runner in Kubernetes
- Create a Kubernetes namespace:
```
kubectl create namespace host-runner
```
- Create secrets (replace placeholder values):
```
kubectl -n host-runner create secret generic github-secret \
  --from-literal=GITHUB_OWNER=<your-github-username> \
  --from-literal=GITHUB_REPOSITORY=<your-repo-name> \
  --from-literal=GITHUB_PERSONAL_TOKEN=<your-github-personal-access-token>
```
Step 7: Deploying to Kubernetes
- Apply the Kubernetes deployment:
```
kubectl apply -f github-runner.yaml -n host-runner
```
- Verify the deployment:
```
kubectl get pods -n host-runner
```
Step 8: Testing the Runner with Nginx Deployment
- Add GitHub Repository Secrets
Ensure the following secrets are added to your GitHub repository for secure handling:
```
DOCKER_REGISTRY: Docker registry name (e.g., Docker Hub username).
DOCKER_USERNAME: Docker Hub username.
DOCKER_PASSWORD: Docker Hub password.
KUBE_CONFIG: Kubernetes configuration file.
```

- Kubernetes Configuration File
To get the KUBE_CONFIG file, you can use the following command:
```
cat /etc/rancher/k3s/k3s.yaml
```
- To get the master node ip, you can use the following command:
```
kubectl get nodes -o wide
```
- Commit and Push to Repository
After creating the required files and configuration, commit and push all files to the main branch of your repository.

Step 9: Verify the Workflow
- Create a directory structure for the Nginx deployment files in your repository root directory.
```
cd ../nginx-deployment
```
- Commit and Push to Repository
After creating the required files and configuration, commit and push all files to the main branch of your repository.

- Access the Application
In Poridhi's Kubernetes cluster verify the deploymets and services
```
kubectl get namespaces
````
- For successful deployment, you should see the dev namespace.
```
kubectl get deployments -n dev
```
- For successful deployment, you should see the nginx-deployment.
```
kubectl get services -n dev
```
- Now to access the application, you should get the etho ip of the master node and the nodeport of the service.

To get the eth0 ip of the master node, you can use the following command:
```
ifconfig
```
- Access the Application:
```
curl ip-address:30080
```

Congratulations!! You have successfully set up a self-hosted runner in Kubernetes and deployed an Nginx application using GitHub Actions. You have learned how to automate the Docker build, Kubernetes deployment, and service creation using GitHub workflows. By leveraging self-hosted runners, you can optimize resource usage and streamline CI/CD processes in Kubernetes environments.
