
### Phase 5: Deploy a development branch
Often times changes will not be so trivial that they can be pushed directly to the staging environment. In order to create a development environment from a long lived feature branch
all you need to do is push it up to the Git server and let Jenkins deploy your environment. In this case you will not use a loadbalancer so you'll have to access your application using `kubectl proxy`,
which authenticates itself with the Kuberentes API and proxies requests from your local machine to the service in the cluster without exposing your service to the internet.

#### Deploy the development branch

1. Create another branch and push it up to the Git server

   ```shell
   $ git checkout -b new-feature
   $ git push origin new-feature
   ```

1. Open Jenkins in your web browser and navigate to the sample-app job. You should see that a new job called "new-feature" has been created and your environment is being created.

1. Navigate to the console output of the first build of this new job by:

  * Click the `new-feature` link in the job list.
  * Click the `#1` link in the Build History list on the left of the page.
  * Finally click the `Console Output` link in the left navigation.

1. Scroll to the bottom of the console output of the job, and you will see instructions for accessing your environment:

   ```
   deployment "gceme-frontend-dev" created
   [Pipeline] echo
   To access your environment run `kubectl proxy`
   [Pipeline] echo
   Then access your service via http://localhost:8001/api/v1/proxy/namespaces/new-feature/services/gceme-frontend:80/
   [Pipeline] }
   ```

#### Access the development branch

1. Open a new Google Cloud Shell terminal by clicking the `+` button to the right of the current terminal's tab, and start the proxy:

   ```shell
   $ kubectl proxy
   ```

1. Return to the original shell, and access your application via localhost:

   ```shell
   $ curl http://localhost:8001/api/v1/proxy/namespaces/new-feature/services/gceme-frontend:80/
   ```

1. You can now push code to the `new-feature` branch in order to update your development environment.

1. Once you are done, merge your `new-feature ` branch back into the  `staging` branch to deploy that code to the staging environment:

   ```shell
   $ git checkout staging
   $ git merge new-feature
   $ git push origin staging
   ```

1. When you are confident that your code won't wreak havoc in production, merge from the `staging` branch to the `master` branch. Your code will be automatically rolled out in the production environment:

   ```shell
   $ git checkout master
   $ git merge staging
   $ git push origin master
   ```

1. When you are done with your development branch, delete it from the server and delete the environment in Kubernetes:

   ```shell
   $ git push origin :new-feature
   $ kubectl delete ns new-feature
   ```

## Extra credit: deploy a breaking change, then roll back
Make a breaking change to the `gceme` source, push it, and deploy it through the pipeline to production. Then pretend latency spiked after the deployment and you want to roll back. Do it! Faster!

Things to consider:

* What is the Docker image you want to deploy for roll back?
* How can you interact directly with the Kubernetes to trigger the deployment?
* Is SRE really what you want to do with your life?
