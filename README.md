![](logo.png)

# gitops

The `gitops` repository has 2 main section

- `/registry`: the argocd gitops app registry 
- `/terraform`: infrastructure as code & configuration as code

## kubefirst apps

The [kubefirst cli](https://github.com/kubefirst/kubefirst) has established the following applications:

| Application              | Namespace        | Description                                 | URL (where applicable)                              |
|--------------------------|------------------|---------------------------------------------|-----------------------------------------------------|
| GitHub           | N/A  | GitHub hosted git                           | https://github.com/rcompos/gitops                                           |
| Vault                    | vault            | Secrets Management                          | http://localhost:8200                                         |
| Argo CD                  | argocd           | GitOps Continuous Delivery                  | http://localhost:8080                                       |
| Argo Workflows           | argo             | Application Continuous Integration          | http://localhost:2746                                |
| Atlantis                 | atlantis         | Terraform Workflow Automation               | http://localhost:4141                                      |
| Chart Museum             | chartmuseum      | Helm Chart Registry                         | http://localhost:8181                                   |
| Metaphor Development     | development      | Development instance of sample application  | http://localhost:4000                                |
| Metaphor Staging         | staging          | Staging instance of sample application      | http://localhost:4001                            |
| Metaphor Production      | production       | Production instance of sample application   | http://localhost:4002                               |
| Nginx Ingress Controller | ingress-nginx    | Ingress Controller                          |                                                     |
| Cert Manager             | cert-manager     | Certificate Automation Utility              |                                                     |
| Certificate Issuers      | clusterwide      | Let's Encrypt browser-trusted certificates  |                                                     |
| External Secrets         | external-secrets | Syncs Kubernetes secrets with Vault secrets |                                                     |
| GitHub Action Runner             | github-runner  | Self Hosted GitHub Action Runner                    |                                                     |

## argocd registry

The argocd configurations in this repo can be found in the [registry directory](./registry). The applications that you build and release on the kubefirst platform will also be registered here in the development, staging, and production folders. The `metaphor` app can be found there to serve as an example to follow.

The `main` branch of this repo represents the desired state all apps registered with kubernetes. Argo CD will automatically try to converge your desired state with the actual state in kubernetes with a process called Argo Sync. You can see the Sync status of all of your apps in the [argo cd ui](http://localhost:8080).

## terraform infrastructure as code

The terraform in this repository can be found in the `/terraform` directory. 

All of our terraform is automated with atlantis. To see the terraform entry points and under what circumstance they are triggered, see [atlantis.yaml](./atlantis.yaml).

Any change to a `*.tf` file, even a whitespace change, will trigger its corresponding Atlantis workflow once a merge request is submitted in GitLab. Within a minute it will post the plan to the pull request with instruction on how to apply the plan if approved.

## terraform configuration as code

In addition to infrastructure terraform, the `gitops` repository also contains configuration as code for the following products:
- ArgoCD: The Argo CD app-registry, repositories, and secrets
- GitLab: Gitlab Repositories and ECR registries needed to house containers for those repositories
- Vault: auth backends, secrets engine, infrastructure secrets

## engineering onboarding

Your kubefirst platform comes with some terraform in place for managing [admins](./terraform/users/admins-github.tf) and [developers](./terraform/users/developers-github.tf). At the top of these two files, you'll find a list of sample admins and developers. Replace this list with the list of actual users you want added to the admin and developer groups and open a pull request. The pull request will show you the user changes in the terraform plan. When approved, have atlantis apply the plan with an `atlantis apply` comment in the pull request.

Your new users will have temporary passwords generated for them and stored in Vault in the `/users` secret store.

