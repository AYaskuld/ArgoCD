1. Install the Argo Rollouts controller
2. Install Ambassador
Blue/Green deployments can work on any vanilla Kubernetes cluster. But for Canary deployments you need a smart service layer that can gradually shift traffic to the canary pods while still keeping the rest of the traffic to the old/stable pods.

Argo Rollouts supports several service meshes and gateways for this purpose.

In this lesson we will use the popular Ambassador API Gateway for Kubernetes to split live traffic between the canary and old/stable version.

We installed Argo CD for you and you can login in the UI tab.

Click the "New app" button on the top left and fill the following details:

application name : ambassador
project: default
SYNC POLICY: automatic
AUTO-CREATE Namespace: enabled
repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
path: ./ambassador-chart
Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
Namespace: ambassador
Leave all the other values empty or with default selections. Finally click the Create button. The Ambassador Gateway will be installed on the cluster.

3. Deploy canary-app in argo CD
4. **Canary deployments**
We are now ready to have a Canary deployment with the next version.  

Change the container image of the rollout to the next version with:  

kubectl argo rollouts set image simple-rollout webserver-simple=docker.io/kostiscodefresh/gitops-canary-app:v2.0
We are using kubectl just for illustration purposes. Normally you should follow the GitOps principles and perform a commit to the Git repo of the application. But just for this exercise we will do all actions manually so that you have time to see what happens.

Enter the following to see what Argo Rollouts is doing behind the scenes

kubectl argo rollouts get rollout simple-rollout
After you change the image the following things happen

- Argo Rollouts creates another replicaset with the new version
- The old version is still there and gets live/active traffic
- The canary version gets 30% of the live traffic.
- ArgoCD will mark the application as out-of-sync
- ArgoCD will also mark the health of the application as "suspended" because we have setup the new color to wait
Notice that even though the next version of our application is already deployed, live traffic goes to both new/old versions. You can verify this by looking at the "live traffic" tab.

version1

At this point the deployment is suspended because we have used the pause properties in the definition of the rollout.

To manually promote the deployment and switch 60% to the new version enter:

kubectl argo rollouts promote simple-rollout
Then monitor again the rollout with

kubectl argo rollouts get rollout simple-rollout --watch
Repeat the same process three more time to send 100% to the canary version. Notice that at each step you can also look at the "stable" and "unstable" tabs and you can see that you can keep both old and new versions in play. This is useful if for some reason you have other applications in the cluster that always need to be pointed to the old or new version while a canary is in progress.

After a while you should see the pods of the old version getting destroyed.

Now all live traffic goes to the new version as can be seen from the "live traffic" tab.

version1

The deployment has finished successfully now.

Finish
