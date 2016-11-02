### Connect to Jenkins

TODO(askcarter):  Move service into this section?

Now find the load balancer IP address of your Ingress service (in the `Address` field). **This field may take a few minutes to appear as the load balancer is being provisioned**:

```shell
$  kubectl get ingress --namespace jenkins
NAME      RULE      BACKEND            ADDRESS         AGE
jenkins      -         master:8080        130.X.X.X      4m
```

The loadbalancer will begin health checks against your Jenkins instance. Once the checks go to healthy you will be able to access your Jenkins instance:
```shell
$  kubectl describe ingress jenkins --namespace jenkins
Name:			jenkins
Namespace:		jenkins
Address:		130.211.14.253
Default backend:	jenkins-ui:8080 (10.76.2.3:8080)
TLS:
  tls terminates
Rules:
  Host	Path	Backends
  ----	----	--------
Annotations:
  https-forwarding-rule:	k8s-fws-jenkins-jenkins
  https-target-proxy:		k8s-tps-jenkins-jenkins
  static-ip:			k8s-fw-jenkins-jenkins
  target-proxy:			k8s-tp-jenkins-jenkins
  url-map:			k8s-um-jenkins-jenkins
  backends:			{"k8s-be-32371":"HEALTHY"}   <---- LOOK FOR THIS TO BE HEALTHY
  forwarding-rule:		k8s-fw-jenkins-jenkins
Events:
  FirstSeen	LastSeen	Count	From				SubobjectPath	Type		Reason	Message
  ---------	--------	-----	----				-------------	--------	------	-------
  2m		2m		1	{loadbalancer-controller }			Normal		ADD	jenkins/jenkins
  1m		1m		1	{loadbalancer-controller }			Normal		CREATE	ip: 130.123.123.123 <--- This is the load balancer's IP
```

Open the load balancer's IP address in your web browser, click "Log in" in the top right and sign in with the default Jenkins username `jenkins` and the password you configured when deploying Jenkins. You can find the password in the `jenkins/k8s/options` file.

> **Note**: To further secure your instance follow the steps found [here](https://wiki.jenkins-ci.org/display/JENKINS/Securing+Jenkins).


![](docs/img/jenkins-login.png)

### Your progress, and what's next
You've got a Kubernetes cluster managed by Google Container Engine. You've deployed:

* a Jenkins Deployment
* a (non-public) service that exposes Jenkins to its slave containers
* an Ingress resource that routes to the Jenkins service

You have the tools to build a continuous deployment pipeline. Now you need a sample app to deploy continuously.

