# Kubernetes containerops demo

## Prerequisites
- Docker
- Docker compose
- Kind

## Set up local test cluster with Kind
From this directory run:
```bash
kind create cluster --name demo --image kindest/node:v1.29.0
```

## Get Kind cluster kubeconfig files
In this directory create a folder "demo" and run:
```bash
kind get kubeconfig --name demo --internal > ./clusters/demo/kubeconfig
```

The --internal flag is needed to use the internal address to the Kubernetes API connecting to the cluster from another docker container in the same bridge network.

## Start the "ops-container"
From this directory run:
```bash
docker compose run demo
```

To rebuild the container use the --build flag:
```bash
docker compose run --build demo
```

At this point you have a running local Kind cluster and a container set up to administer the cluster with the correct CLI versions and a simple z-shell (zsh) set up. Feel free to experiment with this.


## Setting up gitops with Flux CD
Follow this step if you like to [bootstrap Flux CD](https://fluxcd.io/flux/installation/bootstrap/github) to your local Kind cluster. Flux only supports fetching resources from a remote repository, so first you will need to create your own repository in your personal account and copy the contentent of this repo.

Create a github PAT token as described in the [Flux bootstrap guide](https://fluxcd.io/flux/installation/bootstrap/github/#github-personal-account).

From the container started in the previos step, run:

```bash
flux bootstrap github --token-auth --owner=<your-github-user> --repository=<your-repository-name> --branch=main --path=/gitops/clusters/demo --personal
```

You will be asked to provide your GitHub PAT, and Flux will be installed in your cluster.
The installation of Flux will be dependent on the Flux CLI version you have. By updating the version of Flux used defined in the [docker-compose.yaml](./docker-compose.yaml) you can run the bootstrap command again to install a new version of Flux in your cluster.

Flux will overwrite the exsisting flux-system folder under "/gitops/clusters/demo/flux-system" with your set up.

## Test your gitops setup

You can now test to change the log output in the [hello world pod](../gitops/clusters/demo/applications/hello-world/hello-world.yaml) and push to your remote repository.
Flux should reconcile and apply the changes. 

You can check the log in the ops-container:
```bash
kubectl logs deployment/hello -c hello -n hello-world
```
