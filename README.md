# ML Skipper
[![PyPI - Python](https://img.shields.io/badge/python-v3.7+-blue.svg)](https://github.com/ridhimagu/Skipper)
[![GitHub Issues](https://img.shields.io/github/issues/katanaml/katana-skipper.svg)](https://github.com/ridhimagu/Skipper/issues)
[![Current Version](https://img.shields.io/badge/version-1.1.0-green.svg)](https://github.com/ridhimagu/Skipper)

This is a simple and flexible ML workflow engine. It helps to orchestrate events across a set of microservices and create executable flow to handle requests. Engine is designed to be configurable with any microservices.

![Skipper](https://github.com/katanaml/katana-skipper/blob/master/skipper.png)

Engine and Communication parts are generic and can be reused. A group of ML services is provided for sample purposes. You should replace a group of services with your own. The current group of ML services works with Boston Housing data. Data service is fetching Boston Housing data and converts it to the format suitable for TensorFlow model training. Training service builds TensorFlow model. Serving service is scaled to 2 instances and it serves prediction requests.

One of the services, *mobilenetservice*, shows how to use JavaScript based microservice with Skipper. This allows to use containers with various programming languages - Python, JavaScript, Java, etc. You can run ML services with Python frameworks, Node.js or any other choice.


## Instructions

### Start/Stop

#### Docker Compose

Start:

```
docker-compose up --build -d
```

This will start Skipper services and RabbitMQ.

Stop:

```
docker-compose down
```

Web API FastAPI endpoint:

```
http://127.0.0.1:8080/api/v1/skipper/tasks/docs
```

#### Kubernetes

NGINX Ingress Controller:

If you are using local Kubernetes setup, install [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/deploy/)

Build Docker images:

```
docker-compose -f docker-compose-kubernetes.yml build
```

Setup Kubernetes services:

```
./kubectl-setup.sh
```

Skipper API endpoint published through NGINX Ingress (you can setup your own host in /etc/hosts):

```
http://kubernetes.docker.internal/api/v1/skipper/tasks/docs
```

Check NGINX Ingress Controller pod name:

```
kubectl get pods -n ingress-nginx
```

Sample response, copy the name of 'Running' pod:

```
NAME                                       READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-dhtcm       0/1     Completed   0          14m
ingress-nginx-admission-patch-x8zvw        0/1     Completed   0          14m
ingress-nginx-controller-fd7bb8d66-tnb9t   1/1     Running     0          14m
```

NGINX Ingress Controller logs:

```
kubectl logs -n ingress-nginx -f <POD NAME>
```

Remove Kubernetes services:

```
./kubectl-remove.sh
```

### Components

* **[api](https://github.com/ridhimagu/Skipper/tree/master/api)** - Web API implementation
* **[workflow](https://github.com/ridhimagu/Skipper/tree/master/workflow)** - workflow logic
* **[services](https://github.com/ridhimagu/Skipper/tree/master/services)** - a set of sample microservices, you should replace this with your own services. Update references in docker-compose.yml
* **[rabbitmq](https://github.com/ridhimagu/Skipper/tree/master/rabbitmq)** - service for RabbitMQ broker
* **[skipper-lib](https://github.com/ridhimagu/Skipper/tree/master/skipper-lib)** - reusable Python library to streamline event communication through RabbitMQ
* **[skipper-lib-js](https://github.com/ridhimagu/Skipper/tree/master/skipper-lib-js)** - reusable Node.js library to streamline event communication through RabbitMQ
* **[logger](https://github.com/ridhimagu/Skipper/tree/master/logger)** - logger service

### API URLs

* Web API:

```
http://127.0.0.1:8080/api/v1/skipper/tasks/docs
```

If running on local Kubernetes with Docker Desktop:

```
http://kubernetes.docker.internal/api/v1/skipper/tasks/docs
```

* RabbitMQ:

```
http://localhost:15672/ (skipper/welcome1)
```

If running on local Kubernets, make sure port forwarding is enabled:

```
kubectl -n rabbits port-forward rabbitmq-0 15672:15672
```

## Skipper Library on PyPI

* **[PyPI](https://pypi.org/project/skipper-lib/)** - skipper-lib is on PyPI

## Usage

You can use Skipper engine to run Web API, workflow and communicate with a group of ML microservices implemented under services package.

Skipper can be deployed to any Cloud vendor with Kubernetes or Docker support. You can scale Skipper runtime on Cloud using Kubernetes commands.
