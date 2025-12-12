echo "Building VS Code image..."
docker build -f Dockerfile.mlp.ray-vscode -t vscode-ray:test .

echo "Starting VS Code server..."
docker run -d \
  --name vscode-test \
  -p 8080:8080 \
  -e PASSWORD=test123 \
  -v $(pwd)/test-workspace:/home/coder/workspace \
  vscode-ray:test

echo "Waiting for server to start..."
sleep 5

echo "VS Code server is running at http://localhost:8080"
echo "Password: test123"
echo ""
echo "To check logs: docker logs vscode-test"
echo "To stop: docker stop vscode-test && docker rm vscode-test"



For VSCode:
```
export DOCKERFILE=Dockerfile.mlp.ray-vscode
export IMAGE_NAME=ray-vscode
export IMAGE_VERSION=0.0.4
```

```
export DOCKERFILE=Dockerfile.mlp.ray-vscode.2
export IMAGE_NAME=ray-vscode
export IMAGE_VERSION=0.0.5
```

```
docker build -f ${DOCKERFILE} -t ${IMAGE_NAME}:${IMAGE_VERSION} .
```

Test the image:

```
docker run -d \
  --name vscode-test \
  -p 8080:8080 \
  -e PASSWORD=welcome \
  -v ~/.ssh:/home/coder/.ssh:ro \
  ${IMAGE_NAME}:${IMAGE_VERSION}
```

```
docker exec -it vscode-test bash
```

Push the image to mlp repo:
```
export AWS_REGION=us-east-2
export ACCOUNT=<YOUR_ACCOUNT_ID>
export ECR_REPO_NAME=mlp
```

```
aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
```

Tag the images to AWS ECR repo:
```
docker tag \
  ${IMAGE_NAME}:${IMAGE_VERSION} \
  ${ACCOUNT}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}/${IMAGE_NAME}:${IMAGE_VERSION}
```

Create the ECR repository (if needed):
```
aws ecr create-repository \
    --region ${AWS_REGION} \
    --repository-name ${ECR_REPO_NAME}/${IMAGE_NAME}
```

Push the image:
```
docker push ${ACCOUNT}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}/${IMAGE_NAME}:${IMAGE_VERSION}
```


kubectl port-forward svc/raycluster-kuberay-head-svc 8080:8080

kubectl port-forward svc/raycluster-kuberay-head-svc 8265:8265
