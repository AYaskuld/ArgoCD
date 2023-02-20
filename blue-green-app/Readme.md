Before we get started with progressive delivery we need to install the Argo Rollouts controller on the cluster.  

We installed Argo CD for you and you can login in the UI tab.  

The UI starts empty because nothing is deployed on our cluster. Click the "New app" button on the top left and fill the following details:  

application name : argo-rollouts-controller  
project: default  
SYNC POLICY: automatic  
AUTO-CREATE Namespace: enabled  
repository URL: https://github.com/codefresh-contrib/gitops-certification-examples  
path: ./argo-rollouts-controller  
Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)  
Namespace: argo-rollouts  

---

Argo Rollouts also has an optional CLI that can be used for monitoring and promoting deployments  

We have already installed kubectl argo rollouts for you in this exercise. And we can use it to monitor the first deployment  

Run the following  
```bash
kubectl argo rollouts list rollouts
kubectl argo rollouts status simple-rollout
kubectl argo rollouts get rollout simple-rollout
```
The last command shows the status of the rollout. Since this is the first version there is only one replicaset with two pods.  

You can also see this with  
```bash
kubectl get rs
```
---

## Blue/Green deployments  
We are now ready to have a blue/Green deployment with the next version.  

Change the container image of the rollout to the next version with:  
```bash
kubectl argo rollouts set image simple-rollout webserver-simple=docker.io/kostiscodefresh/gitops-canary-app:v2.0
```
We are using kubectl just for illustration purposes. Normally you should follow the GitOps principles and perform a commit to the Git repo of the application.
But just for this exercise we will do all actions manually so that you have time to see what happens.

Enter the following to see what Argo Rollouts is doing behind the scenes  
```bash
kubectl argo rollouts get rollout simple-rollout
```
After you change the image the following things happen  

- Argo Rollouts creates another replicaset with the new version
- The old version is still there and gets live/active traffic
- ArgoCD will mark the application as out-of-sync
- ArgoCD will also mark the health of the application as "suspended" because we have setup the new color to wait

Notice that even though the next version of our application is already deployed, all live traffic goes to the old version.
You can verify this by looking at the "live 

At this point the deployment is suspended because we have used the autoPromotionEnabled: false property in the definition of the rollout.  

To manually promote the deployment and switch all traffic to the new version enter:  
```bash
kubectl argo rollouts promote simple-rollout
``` 
Then monitor again the rollout with  
```bash
kubectl argo rollouts get rollout simple-rollout --watch
```
After a while you should see the pods of the old version getting destroyed.  

Now all live traffic goes to the new version as can be seen from the "live traffic" tab.  

The deployment has finished successfully now.  
