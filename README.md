# Ruby Application Deployment

This demo is for anyone who want to dockerize a ruby application and deploy it using either docker-compose or kubernetes.

### Our Application Architecture

![ ](https://wpblog.semaphoreci.com/wp-content/uploads/2020/02/Getting-Started.png)

### Building Docker Images

1. Build application image:

``` docker build . -f Dockerfile.production ```

2. Build nginx image:

``` docker build . -f Dockerfile.nginx ```

### Pushing docker images to dockerhub

1. For our application images:

``` docker tag image-id DOCKER_HUB_USERNAME/APP_NAME ```

**In my case**

``` docker tag f49520733145 beshlawy38/drkiq:prod ```
>
``` docker push beshlawy38/drkiq:prod ```

2. For nginx image:

``` docker tag image-id DOCKER_HUB_USERNAME/APP_NAME  ```

**In my case**

``` docker tag f5212300d67f beshlawy38/drkiq-nginx  ```
>
``` docker push beshlawy38/drkiq:drkiq-nginx  ```

### Deploying our application using docker compose

##### First we need to change few things:

1. Change the created images in the docker compose file.
2. Create the needed volumes.

 ``` docker volume create --name drkiq-postgres  ```

 ``` docker volume create --name drkiq-redis  ```

##### Then we run: 

 ``` docker-compose up  ```

**You will notice that the drkiq_1 container threw an error saying the database doesn’t exist. This is a completely normal error to expect when running a Rails application because we haven’t initialized the database yet.**

##### Just hit CTRL+C in the terminal to stop everything and run the following commands to initialize the database:

``` docker­-compose run drkiq rake db:reset ```

``` docker­-compose run drkiq rake db:migrate  ```

``` docker-compose up  ```

### Testing docker compose deployment

``` http://localhost:8020 ```

### Deploying our application using Kubernetes

##### I'm using minikube. You can use the following link to install minikube on your machine.

https://minikube.sigs.k8s.io/docs/start/

##### We need to run the following commands:

``` cd k8s ```

``` kubectl apply -f . ```

##### You should see the following:

deployment.apps/drkiq created <br />
persistentvolumeclaim/drkiq-postgres created <br />
persistentvolumeclaim/drkiq-redis created <br />
service/drkiq created <br />
configmap/env created <br />
deployment.apps/nginx created <br />
service/nginx created <br />
deployment.apps/postgres created <br />
secret/postgres-secret created <br />
service/postgres created <br />
deployment.apps/redis created <br />
service/redis created <br />
deployment.apps/sidekiq created <br />

##### One last thing like we did it before when using docker compose, we need to intiallize our database:

``` kubectl exec drkiq-pod-id -it -- rake db:reset ```

``` kubectl exec drkiq-pod-id -it -- rake db:migrate ```

### Testing kubernetes deployment

``` kubectl port-forward svc/nginx 8020:8020 ```

``` http://localhost:8020 ```

### Reference

https://semaphoreci.com/community/tutorials/dockerizing-a-ruby-on-rails-application