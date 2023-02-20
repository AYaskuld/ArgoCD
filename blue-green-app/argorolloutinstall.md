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
