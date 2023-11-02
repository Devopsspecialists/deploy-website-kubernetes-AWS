## Create and run Dockerfile locally.
1. docker build -t explorecalifornia.com .
2. docker run --rm --name explorecalifornia.com -p 5000:80 explorecalifornia.com

## install kind to create cluster
1. curl --location --output ./kind https://github.com/kubernetes-sigs/kind/releases/download/v0.20.0/kind-linux-amd64
2. chmod +x ./kind

## Create kind cluster
./kind create cluster --name explorecalifornia.com --config kind_config.yaml || true

## create a local registry
1. docker run --name local-registry -d --restart=always -p 5000:5000 registry:2
2. curl http://127.0.0.1:5000/v2
3. curl --location http://127.0.0.1:5000/v2

## Linking the local Docker registry to the kind cluster
1. docker network connect kind local-registry || true
2. kubectl apply -f kind_configmap.yaml

## Pushing docker image to docker-registry
1. docker tag explorecalifornia.com 127.0.0.1:5000/explorecalifornia.com
2. docker push 127.0.0.1:5000/explorecalifornia.com
## create and test deployment
1. kubectl apply -f deployment.yaml
2. kubectl port-forward deployments/explorecalifornia.com 8080:80
## create service
1. kubectl apply -f service.yaml
2. kubectl port-forward service/explorecalifornia-svc 8080:80
## create ingress controller
1. kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
2. sudo vim /etc/hosts  and add '127.0.0.1 explorercalifornia.com' to hosts




