### Deploy the sample app to Kubernetes
In this section you will deploy the `gceme` frontend and backend to Kubernetes using Kubernetes manifest files (included in this repo) that describe the environment that the `gceme` binary/Docker image will be deployed to. They use a default `gceme` Docker image that you will be updating with your own in a later section.

You'll have two primary environments - staging and production - and use Kubernetes namespaces to isolate them.

> **Note**: The manifest files for this section of the tutorial are in `sample-app/k8s`. You are encouraged to open and read each one before creating it per the instructions.

1. First change directories to the sample-app:

  ```shell
  $ cd sample-app
  ```

1. Create the namespace for production:

  ```shell
  $ kubectl create ns production
  ```

1. Create the staging and production Deployments and Services:

    ```shell
    $ kubectl --namespace=production apply -f k8s/production
    $ kubectl --namespace=production apply -f k8s/canary
    $ kubectl --namespace=production apply -f k8s/services
    ```

1. Scale the production service:

    ```shell
    $ kubectl --namespace=production scale deployment gceme-frontend-production --replicas=4
    ```

1. Retrieve the External IP for the production services: **This field may take a few minutes to appear as the load balancer is being provisioned**:

  ```shell
  $ kubectl --namespace=production get service gceme-frontend
  NAME             CLUSTER-IP      EXTERNAL-IP      PORT(S)   AGE
  gceme-frontend   10.79.241.131   104.196.110.46   80/TCP    5h
  ```

1. Confirm that both services are working by opening the frontend external IP in your browser

1. Open a new Google Cloud Shell terminal by clicking the `+` button to the right of the current terminal's tab, and poll the production endpoint's `/version` URL. Leave this running in the second terminal so you can easily observe rolling updates in the next section:

   ```shell
   $ export FRONTEND_SERVICE_IP=$(kubectl get -o jsonpath="{.status.loadBalancer.ingress[0].ip}"  --namespace=production services gceme-frontend)
   $ while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1;  done
   ```

1. Return to the first terminal

### Create a repository for the sample app source
Here you'll create your own copy of the `gceme` sample app in [Cloud Source Repository](https://cloud.google.com/source-repositories/docs/).

1. Change directories to `sample-app` of the repo you cloned previously, then initialize the git repository.

   **Be sure to replace _REPLACE_WITH_YOUR_PROJECT_ID_ with the name of your Google Cloud Platform project**

    ```shell
    $ cd sample-app
    $ git init
    $ git config credential.helper gcloud.sh
    $ git remote add origin https://source.developers.google.com/p/REPLACE_WITH_YOUR_PROJECT_ID/r/default
    ```

1. Ensure git is able to identify you:

    ```shell
    $ git config --global user.email "YOUR-EMAIL-ADDRESS"
    $ git config --global user.name "YOUR-NAME"
    ```

1. Add, commit, and push all the files:

    ```shell
    $ git add .
    $ git commit -m "Initial commit"
    $ git push origin master
    ```
