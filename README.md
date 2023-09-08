# Falco-demo

This initiative demonstrates the implementation of Falco as a container runtime security solution. The Falco project can be found at https://github.com/falcosecurity. 

## Instructions:

Falco is deployed to a Kubernetes cluster (AWS Elastic Kubernetes Service (EKS), Azure Kubernetes Service (AKS), Google Kubernetes Engine (GKE), etc.) through **Helm**.

### Adding the `falcosecurity` repository:

Before installing the chart, add the `falcosecurity` charts repository:

```bash
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm repo update
```

### To install the Chart:

**Please note:** The default setting in the chart values inside the helm repo is for the **sidekick** to be disabled. The sidekick performs a powerful function by channeling syscalls that were captured by the driver to any of the large number of integration services available. From there the syscall events can either be visualized (as demonstrated by this initiative) or trigger a further response in the form of alerts and possible custom actions.   

The `values_falco.yml` file in this repo contains configuration settings to have the following resources deployed through **helm**:
 - Falco-driver
 - Falco sidekick
 - Falcosidekick UI Redis
 - Falcosidekick UI

Clone this repo to ensure there is a local copy of the `values_falco-demo.yml` available to be used in the next step.

To install the chart with the release name `falco` in namespace `falco` run:

```bash
helm install falco falcosecurity/falco --namespace falco --create-namespace -f <path_to_values_falco-demo.yml>
```

To view the resources that have been deployed:
```bash
kubectl -n falco get pods

NAME                                             READY   STATUS    RESTARTS   AGE
falco-driver-6c9854888f-mf67f                    1/1     Running   0          1m
falco-driver-6c9854888f-n5kn5                    1/1     Running   0          1m
falco-driver-falcosidekick-dd4cddbff-44czl       1/1     Running   0          1m
falco-driver-falcosidekick-ui-549c596575-bhchh   1/1     Running   0          1m
falco-driver-falcosidekick-ui-redis-0            1/1     Running   0          1m
```

The dashboard provides an overview of syscalls captured by the Falco-drivers and can be accesed as follows:
```bash
kubectl -n falco port-forward svc/falco-falcosidekick-ui 2802
```

From your browser, use the following URL: http://localhost:2802/. The default username and password is admin, but can be changed in the `values_falco-demo.yml` file.

The sidekick-UI is an example of a Falco integration option with the Falco-driver.

Refer to https://github.com/falcosecurity/charts/blob/master/README.md for any additional information on the charts and configuration options.









