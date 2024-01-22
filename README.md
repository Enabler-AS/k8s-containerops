# K8S ContainerOps

When working with and managing Kubernetes clusters we are communicating with APIs. These APIs typically follow a deprecation policy (e.g. see [kubernetes deprecation policy](https://kubernetes.io/docs/reference/using-api/deprecation-policy/), and our operations environment containing the tools we need to manage our clusters should closely follow the Kubernetes environments as it evolves.

This is a simple example of how you can set up an "ops-container" for managing a Kubernetes cluster environment.

The [container](./connect/Dockerfile) is set up with tools for managing Kubernetes cluster with Flux:

- [Kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [Flux CLI](https://fluxcd.io/flux/cmd/)
- [k9s](https://k9scli.io/)
- Simple Z shell (zsh) set up 

## Test locally

Go through the steps in [README.md](./connect/README.md) to set up a local kubernetes cluster with Kind and use a "ops-container" to manage and access the cluster.