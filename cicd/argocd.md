# ArgoCD

## login using the CLI

initial password for the admin account is auto-generated and stored as clear text in the field password in secret named `argocd-initial-admin-secret`.

you can simply retrieve this password using the argocd CLI

```bash
argocd admin initial-password -n argocd
```

login to Argo CD's IP or hostname

```bash
argocd login <ARGOCD_SERVER>
```

## change the password

```bash
argocd account update-password
```

## Register A Cluster to Deploy Apps

registers a cluster's credentials to Argo CD, and is only necessary when deploying to an external cluster. When deploying internally (to the same cluster that Argo CD is running in) http://kubernetes.default.svc should be used as the application's k8s API server address.

list all clusters contexts in your current kubeconfig.

`kubectl config get-contexts -o name`

choose a context name from the list and supply it to argocd cluster add CONTEXTNAME.

`argocd cluster add docker-desktop`

This command installs a ServiceAccount (argocd-manager), into the kube-system namespace of that kubectl context, and binds the service account to an admin-level ClusterRole. Argo CD uses the service account token to perform its management tasks.

## Create an Application from a Git Repository

```bash
$ k config set-context --current --namespace=argocd

$ argocd app create guestbook \
--repo https://github.com/argoproj/argocd-example-apps.git \
--path guestbook \
--dest-server https://kubernetes.default.svc \ 
--dest-namespace default
```

## Sync (Deploy) the application

```bash
# to view status.
$ argocd app get guestbook
The application status is initially in OutOfSync state since the application has yet to be deployed and no Kubernetes resources have been created. To sync(deploy) the application run

$ argocd app sync guestbook
```


