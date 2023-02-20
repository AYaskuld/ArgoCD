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
