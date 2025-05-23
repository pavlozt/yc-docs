# Creating a network load balancer using an NGINX Ingress controller

When installing an [NGINX Ingress controller](https://kubernetes.github.io/ingress-nginx/), you can create an [external](../../network-load-balancer/concepts/index.md) or [internal](../../network-load-balancer/concepts/nlb-types.md) network load balancer and set up [port forwarding](#port-forwarding).

## Getting started {#before-you-begin}

1. [Create a service account](../../iam/operations/sa/create.md) with the `k8s.clusters.agent`, `vpc.publicAdmin`, `container-registry.images.puller`, and `load-balancer.admin` [roles](../../iam/concepts/access-control/roles.md) for the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder). The `load-balancer.admin` role is required to create a [network load balancer](../../network-load-balancer/concepts/index.md).
1. {% include [configure-sg-manual](../../_includes/managed-kubernetes/security-groups/configure-sg-manual-lvl3.md) %}

    {% include [sg-common-warning](../../_includes/managed-kubernetes/security-groups/sg-common-warning.md) %}

1. [Create a {{ managed-k8s-name }} cluster](kubernetes-cluster/kubernetes-cluster-create.md). In the cluster settings, specify the service account and the security groups created earlier.
1. {% include [Install Helm](../../_includes/managed-kubernetes/helm-install.md) %}
1. {% include [Install and configure kubectl](../../_includes/managed-kubernetes/kubectl-install.md) %}
1. Set up Helm to work with the NGINX repository:

   1. Add a repository for NGINX to Helm:

      ```bash
      helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
      ```

      Result:

      ```text
      "ingress-nginx" has been added to your repositories
      ```

   1. Update the dataset to create an application instance in the [{{ managed-k8s-name }} cluster](../concepts/index.md#kubernetes-cluster):

      ```bash
      helm repo update
      ```

      Result:

      ```text
      Hang tight while we grab the latest from your chart repositories...
      ...Successfully got an update from the "ingress-nginx" chart repository
      Update Complete. ⎈Happy Helming!⎈
      ```

   
   {% note tip %}

   You can also install an NGINX Ingress controller [using {{ marketplace-full-name }}](applications/ingress-nginx.md).

   {% endnote %}


## External network load balancer {#external}

To create a network load balancer, the service account linked to your {{ managed-k8s-name }} cluster must have the `load-balancer.admin` role.

An external network load balancer is created when installing an NGINX Ingress controller in a standard configuration:

```bash
helm install ingress-nginx ingress-nginx/ingress-nginx
```

Result:

```text
NAME: ingress-nginx
LAST DEPLOYED: Sun Jul 18 22:35:37 2022
NAMESPACE: default
...
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w ingress-nginx-controller'
...
```

## Internal network load balancer {#internal}

To create a network load balancer, the service account linked to your {{ managed-k8s-name }} cluster must have the `load-balancer.admin` role.

To install an internal network load balancer:
1. [Configure the controller](https://github.com/kubernetes/ingress-nginx/blob/main/charts/ingress-nginx/values.yaml). To do this, create a configuration file named `values.yaml` and specify in it the ID of the [subnet](../../vpc/concepts/network.md#subnet) the network load balancer should operate in:

   ```yaml
   controller:
     service:
       external:
         enabled: false
       internal:
         enabled: true
         annotations:
           yandex.cloud/load-balancer-type: internal
           yandex.cloud/subnet-id: <subnet_ID>
   ```

1. Install an NGINX Ingress controller using the `values.yaml` configuration file:

   ```bash
   helm install ingress-nginx -f values.yaml ingress-nginx/ingress-nginx
   ```

   Result:

   ```text
   NAME: ingress-nginx
   LAST DEPLOYED: Sun Jul 18 22:55:37 2022
   NAMESPACE: default
   ...
   The ingress-nginx controller has been installed.
   It may take a few minutes for the LoadBalancer IP to be available.
   You can watch the status by running 'kubectl --namespace default get services -o wide -w ingress-nginx-controller'
   ...
   ```

## Check the result {#check-result}

To make sure the network load balancer was created, get a [list of network load balancers in the folder](../../network-load-balancer/operations/load-balancer-list.md#list).

## Port forwarding {#port-forwarding}

Even though NGINX Ingress controllers officially support external HTTP and HTTPS traffic routing only, you can configure them to accept external TCP or UDP traffic and redirect it to internal services. To do this, install an Ingress controller using the `values.yaml` [configuration file](https://github.com/kubernetes/ingress-nginx/blob/main/charts/ingress-nginx/values.yaml) with traffic redirect settings and `portNamePrefix`.
1. Create a file named `values.yaml`:

   ```yaml
   <protocol>: {<external_port>: "<service_namespace>/<service_name>:<internal_port>"}
   portNamePrefix: "<prefix>"
   ```

   Where `<protocol>` is the protocol, `tcp` or `udp`.

1. Install a NGINX Ingress controller using the `values.yaml` configuration file:

   ```bash
   helm install ingress-nginx -f values.yaml ingress-nginx/ingress-nginx
   ```

>Example
>
>Let's say we need to set up traffic forwarding with the following parameters:
>* Service name: `example-go`.
>* Service namespace: `default`.
>* Internal service port: `8080`.
>* External port: `9000`.
>* Port name prefix: `test`.
>
>`values.yaml` configuration file for such forwarding:
>
>```yaml
>tcp: {9000: "default/example-go:8080"}
>portNamePrefix: "test"
>```

After you install the Ingress controller, the new network load balancer will have an additional listener, `test-9000-tcp`, with the forwarding settings you specified.

The names of the NGINX Ingress controller's port and network load balancer's listener are based on the forwarding settings: `<external_port>-<protocol>`. Due to {{ yandex-cloud }}'s limitations, the listener's name may not start with numbers, so prefix it with `portNamePrefix` to ensure the settings are correct. The port and listener names will thus be generated in `<portNamePrefix_value>-<external_port>-<protocol>` format.

Due to technical limitations, the port name is limited to 15 characters and the listener name must not start with numbers. Therefore, make sure that your `portNamePrefix` prefix:
* Starts with letters.
* Is no longer than 5-8 characters, depending on the external port value length.

To make sure that port forwarding is configured properly, view the list of listeners in the [network load balancer details](../../network-load-balancer/operations/load-balancer-list.md#get).
