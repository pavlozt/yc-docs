# Gateway resource fields

The `Gateway` resource defines the rules for accepting incoming traffic and selecting routes (the [HTTPRoute](../../../application-load-balancer/k8s-ref/http-route.md) and [TLSRoute](../../../application-load-balancer/k8s-ref/tls-route.md) resources) for that traffic. [{{ alb-name }} Gateway API](../../../application-load-balancer/tools/k8s-gateway-api/index.md) uses these rules to create:

* [Load balancer](../../../application-load-balancer/concepts/application-load-balancer.md) with the required listeners.
* [Backend groups](../../../application-load-balancer/concepts/backend-group.md).
* [HTTP routers](../../../application-load-balancer/concepts/http-router.md) (if the [HTTPRoute](../../../application-load-balancer/k8s-ref/http-route.md) resources are used).

`Gateway` is designed for cluster operators. Application developers should use `TLSRoute` or `HTTPRoute`.

`Gateway` is a {{ k8s }} resource specified by the [{{ k8s }} Gateway API project](https://gateway-api.sigs.k8s.io/). Below, you can find the descriptions of the resource fields and annotations {{ alb-name }} Gateway API interfaces with. For a full description of the resource configuration, see the [{{ k8s }} Gateway API documentation](https://gateway-api.sigs.k8s.io/references/spec/#gateway.networking.k8s.io/v1alpha2.Gateway).

## Gateway {#gateway}

```yaml
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: Gateway
metadata:
  name: <string>
  namespace: <string>
  annotations:
    gateway.alb.yc.io/security-groups: <string>
spec: <GatewaySpec>
```

Where:

* `apiVersion`: `gateway.networking.k8s.io/v1alpha2`
* `kind`: `Gateway`
* `metadata` (`ObjectMeta`, required)
  
  Resource metadata.

  * `name` (`string`, required)
    
    Resource name. For more information about the format, please see the [{{ k8s }} documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

    This name is not the balancer name in {{ alb-name }}.
  
  * `namespace` (`string`)
  
    [Namespace](../../../managed-kubernetes/concepts/index.md#namespace) the resource belongs to. The default value is `default`.
   
  * `annotations` (`map[string]string`, required)
    
    Resource annotations.

    * `gateway.alb.yc.io/security-groups` (`string`, required)

      List of {{ vpc-name }} [security groups](../../../vpc/concepts/security-groups.md) for a load balancer. Group IDs are provided in a comma-separated list, e.g.:
    
      ```
      gateway.alb.yc.io/security-groups: b0c2kotoidcoh6haf8cu,e2lnhhdj9a0aqmr78d36,e9bud5itjnl8mkjj7td1
      ```
    
      For the load balancer and Gateway API to function properly, make sure to configure security groups as specified in [{#T}](../../../application-load-balancer/tools/k8s-ingress-controller/security-groups.md).

* `spec` (`GatewaySpec`, required)

  Resource specification. For more information, see [below](#spec).


## GatewaySpec {#spec}

```yaml
gatewayClassName: yc-df-class
listeners:
  - name: <string>
    hostname: <string>
    port: <int32>
    protocol: <string>
    tls:
      mode: <string>
      certificateRefs:
        - group: <string>
          kind: <string>
          name: <string>
          namespace: <string>
        - ...
    allowedRoutes:
      namespaces:
        from: <string>
        selector:
          matchExpressions:
            - key: <string>
              operator: <string>
              values:
                - <string>
                - ...
          matchLabels:
            <string>: <string>
            ...
  - ...
addresses:
  - type: IPAddress
    value: <string>
  - ...
```

Where:

* `gatewayClassName`: `yc-df-class`
* `listeners` (`[]Listener`)

  Load balancer listeners.

  * `name` (`string`)
    
    Internal name of the listener.
    
    This name only serves the {{ k8s }} needs and is not the listener name in {{ alb-name }}.
  
    A name should have the domain format, i.e., correspond to the following regular expression: 
    
    ```[a-z0-9]([-a-z0-9]*[a-z0-9])?(\.[a-z0-9]([-a-z0-9]*[a-z0-9])?)*```
  
    For example, `example`, `example.com`, and `foo.example.com` are valid names, while `example.com/bar` and `-example.` are not.
    
    The name can be up to 63 characters long.
    
  * `hostname` (`string`)

    Domain name that the listener is enabled for.

    {% include [k8s-ingress-controller-hostnames-wildcard](../../application-load-balancer/k8s-ingress-controller-hostnames-wildcard.md) %}
  
    Only the routes (the [HTTPRoute](../../../application-load-balancer/k8s-ref/http-route.md) and [TLSRoute](../../../application-load-balancer/k8s-ref/tls-route.md) resources) whose domain names (the `spec.hostnames` field) <q>overlap</q> with the domain name specified in this field will be linked to the listener.

  * `port` (`int32`)
    
    Port the listener uses for incoming traffic.
    
  * `protocol` (`string`)
    
    Protocol the listener uses for incoming traffic: `HTTP`, `HTTPS` or `TLS`.

  * `tls` (`GatewayTlsConfig`)

    TLS settings used for incoming HTTPS or TLS traffic.
  
    * `mode` (`string`)
      
      Mode for terminating TLS connections.
      
      The only supported and default value is `Terminate`: connections are terminated using certificates from the `certificateRefs` field, with decrypted traffic routed to backends. `Passthrough` mode (without connection termination) is not supported.

    * `certificateRefs` (`[]SecretObjectReference`)
  
      List of {{ k8s }} resources where TLS certificates are stored.

      It is only used if the `protocol` field is set to `HTTPS` or `TLS`. In which case the list must contain at least one certificate.

      The load balancer only uses the first certificate from the list while ignoring the other ones.
  
      You can add a certificate to a cluster as a secret (the `Secret` resource) using the {{ managed-k8s-name }} management console or `kubectl`:
  
      ```
      kubectl create secret tls <secret_name> \
        -n <namespace_name> \
        --cert <certificate_file_path> \
        --key <path_to_file_with_certificate_private_key>
      ```
  
      * `group` (`string`)
        
        Name of the {{ k8s }} API group the resource with the certificate belongs to, e.g., `networking.k8s.io`. 
        
        The default value is an empty line that indicates the root API group.

      * `kind` (`string`)
        
        Type of the {{ k8s }} resource that stores the certificate.
  
        The default value is `Secret`.

      * `name` (`string`)
        
        Name of the {{ k8s }} resource that stores the certificate.

      * `namespace` (`string`)
  
        Namespace that the name of the resource with the certificate belongs to.

  * `allowedRoutes` (`AllowedRoutes`)

    Rules for selecting routes for the listener ([HTTPRoute](../../../application-load-balancer/k8s-ref/http-route.md) and [TLSRoute](../../../application-load-balancer/k8s-ref/tls-route.md) resources). To ensure a route is selected, the configuration of these resources must have the `Gateway` resource specified in the `spec.parentRefs` field.

    These routes are used to create the [backend groups](../../../application-load-balancer/concepts/backend-group.md) you can link to the listener. If using `HTTPRoute`, [HTTP routers](../../../application-load-balancer/concepts/http-router.md) are also created.

    * `namespaces` (`RouteNamespaces`)
  
      Rule for selecting namespaces that cover the `HTTPRoute` and `TLSRoute` resources linked to the listener.
  
      * `from` (`string`)
        
        Rule type:
  
        * `All`: Resources are selected from all namespaces.
        * `Same`: Resources are only selected from the same namespace as the `Gateway` resource (the `metadata.namespace` field).
        * `Selector`: Resources are selected from namespaces that meet the criteria specified in the `selector` field.

      * `selector` (`LabelSelector`)
  
        A selector is a set of namespace requirements. Only namespaces that meet all the criteria in the `matchExpressions` and `matchLabels` fields are selected.
  
        To learn more, see the [{{ k8s }} API reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#labelselector-v1-meta).
  
        If the `from` field is not set to `Selector`, the `selector` field is ignored. 

* `addresses` (`[]GatewayAddress`)

  Load balancer's public IP settings.

  If omitted, the load balancer is automatically assigned one public IP address. 

  * `type`: `IPAddress`
  * `value` (`string`)
  
    {{ vpc-full-name }} public IP assigned to the load balancer.
  
    Before specifying an IP address in this field, make sure to reserve it by following [this guide](../../../vpc/operations/get-static-ip.md).
