In this lab, we'll quickly explore Kubernetes and what it offers.

This tutorial assumes familiarity with container images.


## Create a Deployment

```
$ kubectl run monolith --image askcarter/monolith:1.0.0
```

## Expose Deployment for External Access
```
$ kubectl expose deployment monolith --port 80 --type LoadBalancer
```

## Scale up Deployment
```
$ kubectl scale deployment monolith --replicas 3
```

## Access Deployment
```
$ kubectl get service monolith
$ curl http://<External-IP>
```

## Clean Up
```
$ kubectl delete services monolith
$ kubectl delete deployment monolith
```


