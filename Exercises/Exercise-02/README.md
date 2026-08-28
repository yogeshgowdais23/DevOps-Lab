# Exercise 02: Minikube, kubectl and Flask

## Objective

Build a simple Flask web application into a Docker image, deploy it to Kubernetes running on Minikube, expose it through a NodePort Service, and verify that the endpoint returns the expected message.

## Environment

- macOS on Apple Silicon
- Minikube using the Docker Desktop driver
- Kubernetes runtime reported by Minikube: containerd
- `kubectl` and Docker Desktop
- Python base image: `python:3.8-slim`
- Flask application port: `15000`

## Project files

- `app.py` — Flask endpoint returning a greeting.
- `Dockerfile` — packages the Flask application into an image.
- `flask-deployment.yaml` — Kubernetes Deployment and Service resources.
- `screenshots/` — evidence captured during the exercise.

## Procedure

### 1. Start Minikube

```bash
minikube start
```

Minikube started successfully and configured `kubectl` to use the local cluster.

### 2. Create the Flask application

`app.py` defines a `/` route that returns `Hello from Flask on Kubernetes!`. The Flask development server listens on `0.0.0.0:15000` so it can receive traffic inside the container.

### 3. Create the Dockerfile

The Dockerfile uses `python:3.8-slim`, sets `/app` as the working directory, copies the project files, installs Flask, and runs `app.py`.

### 4. Build and load the image

The standard `minikube docker-env` route displayed a warning because this Minikube profile uses the containerd runtime. The image was built using Docker Desktop and loaded into Minikube.

```bash
eval "$(minikube docker-env -u)"
docker context use desktop-linux
docker build -t flask-app .
minikube image load flask-app:latest
```

### 5. Deploy the Flask application

The `Deployment` runs one replica of `flask-app:latest`, exposes container port `15000`, and uses `imagePullPolicy: Never` because the image was loaded locally into Minikube.

```bash
kubectl apply -f flask-deployment.yaml
kubectl get deployments
kubectl get pods -l app=flask-app
kubectl describe deployment flask-app
kubectl logs -l app=flask-app
```

The pod reached `Running` and `1/1` Ready status. The logs showed Flask listening on port `15000`.

### 6. Test before exposing the application

Before the Service was created, this request failed because port `15000` was not published on the Mac loopback interface:

```bash
curl http://127.0.0.1:15000
```

### 7. Expose the application with a Service

The same YAML file contains a NodePort Service named `flask-app-service`, separated from the Deployment by `---`. It selects pods labelled `app: flask-app` and maps Service port `15000` to pod port `15000`.

```bash
kubectl apply -f flask-deployment.yaml
kubectl get services
```

Kubernetes may report the Deployment as `unchanged` when it already exists; this is expected.

### 8. Get the Minikube URL and test

```bash
minikube service flask-app-service --url
```

On macOS with the Docker driver, Minikube printed a local URL and warned that the terminal running the command must remain open to keep the tunnel available. In the captured run, the URL was `http://127.0.0.1:65480`. Use the actual URL printed by your own Minikube session because the port can change.

From another terminal, the request succeeded:

```bash
curl http://127.0.0.1:65480
```

Response:

```text
Hello from Flask on Kubernetes!
```

## Screenshots

Screenshots are in [`screenshots/`](screenshots/) and numbered in workflow order:

1. Minikube startup
2. Flask application source
3. Dockerfile
4. Minikube Docker environment warning
5. Successful Docker image build
6. Deployment YAML
7. Applying the Deployment
8. Deployment status
9. Flask pod running
10. Deployment details
11. Flask container logs
12. Services before Flask exposure
13. Failed curl before exposure
14. Applying the Deployment and Service YAML
15. Minikube Service URL
16. Successful Flask response

## Key concepts

- **Container image:** Packages the application and its runtime dependencies.
- **Deployment:** Maintains the desired number of pod replicas.
- **Pod:** Runs the application's container inside Kubernetes.
- **Service:** Provides a stable network endpoint for selected pods.
- **NodePort:** Exposes a Service through a port on the Kubernetes node.
- **`port` and `targetPort`:** `port` is the Service port; `targetPort` is the destination port on the pod.
- **Image availability:** A locally built image must be available to Minikube's container runtime when `imagePullPolicy: Never` is used.
- **Minikube service tunnel:** On macOS with the Docker driver, the command may need to remain running while the local URL is in use.

## Result

The Flask application was containerized, deployed to Minikube, and exposed through a NodePort Service. The final `curl` request returned `Hello from Flask on Kubernetes!`.
