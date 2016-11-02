The first hurdle to getting online is the application itself. How do you write it? How do you deploy it?

First off let's set up the code and the go build environment

$ GOPATH=~/go
$ mkdir -p $GOPATH/src/github.com/askcarter
$ cd $GOPATH/src/github.com/askcarter
$ git clone https://github.com/askcarter/io16
Now let's build the app as a static binary and test it's functionality.

$ cd io16/app/monolith
$ go build -tags netgo -ldflags "-extldflags '-lm -lstdc++ -static'" .
$ ./monolith --http :10180 --health :10181 &
$ curl http://127.0.0.1:10180
$ curl http://127.0.0.1:10180/secure
$ curl http://127.0.0.1:10180/login -u user
$ curl http://127.0.0.1:10180/secure -H "Authorization: Bearer <token>"
Once we have a binary, we can use Docker to package and distribute it.

$ docker build -t askcarter/monolith:1.0.0 .
$ docker push askcarter/monolith:1.0.0
$ docker run -d askcarter/monolith:1.0.0
$ docker ps
$ docker inspect <cid>
$ curl http://<docker-ip>
$ docker rm <cid>
$ docker rmi askcarter/monolith:1.0.0
The Second Hurdle: The Infra

The next hurdle is the infrastructure needed to run manage in production. We'll use Kubernetes (and GKE) to handle that for us.

$ cd ../../kubernetes
$ gcloud container clusters create io --num-nodes=6
$ kubectl run monolith --image askcarter/monolith:1.0.0
$ kubectl expose deployment monolith --port 80 --type LoadBalancer
$ kubectl scale deployment monolith --replicas 3
$ kubectl get service monolith
$ curl http://<External-IP>
$ kubectl delete services monolith
$ kubectl delete deployment monolith
Let's set up services and deployments for our microservices

$ kubectl create -f services/auth.yaml -f deployments/auth.yaml
$ kubectl create -f services/hello.yaml -f deployments/hello.yaml
$ kubectl create configmap nginx-frontend-conf --from-file nginx/frontend.conf
$ kubectl create secret generic tls-certs --from-file tls/
$ kubectl create -f services/frontend.yaml -f deployments/frontend.yaml
$ kubectl get services frontend
$ curl -k https://<External-IP>
The Third Hurdle: The Wild

The last hurdle is the The Wild. How do we handle rolling out updates to our code?

$ while true; do curl <IP>; sleep .3; done
$ sed -i s/hello:1.0.0/hello:2.0.0/g deployments/hello.yaml
$ kubectl apply -f deployments/hello.yaml
$ kubectl describe deployments hello
Cleanup

$ gcloud container clusters delete io
