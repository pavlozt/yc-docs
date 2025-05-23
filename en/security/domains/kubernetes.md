# Security {{ k8s }}

The section includes recommendations for {{ yandex-cloud }} users on security settings in [{{ managed-k8s-full-name }}](../../managed-kubernetes/).

## Responsibility {#division-of-responsibility}

The user is responsible for all actions made inside the {{ k8s }} node. The user is responsible for the security of the nodes and their proper setup in accordance with PCI DSS requirements and other security standards.

{{ yandex-cloud }} is responsible for the {{ k8s }} API security.

The user is responsible for correctly choosing security settings in {{ managed-k8s-name }}, including selecting the [channel](../../managed-kubernetes/concepts/release-channels-and-updates.md) and the update schedule.  

## Sensitive data {#critical-data}

To comply with PCI DSS or other security standards when using {{ managed-k8s-name }}, do not:

* Use sensitive data in names and descriptions of clusters, node groups, namespaces, services, and pods.
* Use sensitive data in [{{ k8s }} node labels](../../managed-kubernetes/concepts/#node-labels) and [{{ yandex-cloud }} service resource labels](../../resource-manager/concepts/labels.md).
* Use sensitive data in pod manifests.
* Use sensitive data in etcd in clear text.
* Write sensitive data to {{ managed-k8s-name }} logs.

## Resource model {#resource-model}

Wherever possible, ensure maximum isolation between resources:

* Use a separate organization for each _big_ project.
* Use a separate cloud for each development team.
* Use a separate {{ k8s }} cluster located in a separate folder for each service.
* Use a separate namespace for each microservice.

Your clouds must have no shared resources. Cloud members must have access only to their clouds.

Less strong isolation models are also possible, for example:

* Projects are split between different clouds.
* Development teams are assigned independent folders.
* Services have separate {{ k8s }} clusters.
* Microservices have independent namespaces.

## {{ managed-k8s-name }} network security {#network-security}

We do not recommend granting access to the {{ k8s }} API and node groups from non-trusted networks, e.g., from the internet.
Use firewall protection where needed (for example, [security groups](../../vpc/concepts/security-groups.md)). In the section below, you can find links to instructions on how to set up firewall protection in security groups.

### Segmentation {#segmentation}

#### Cloud level {#cloud-level}

Restrict network access to the {{ k8s }} API (master) and node groups using [instructions for security groups](../../managed-kubernetes/operations/connect/security-groups.md).

When using an ALB as an [Ingress Gateway](../../application-load-balancer/tools/k8s-ingress-controller/index.md), also complete the following steps:

1. Apply the security group to the ALB.
2. Additionally, apply the security group to the node group:

    * Source type: `<security group applied for the ALB>`.
    * Destination type: `node group`.
    * Port range: 30000-32767.

#### {{ k8s }} level {#kubernetes-level}

Restrict network access inside {{ k8s }} using the [Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

You can use two network plugins in {{ yandex-cloud }}:

* [Calico](../../managed-kubernetes/concepts/network-policy.md#calico): a basic plugin.
* [Cilium CNI](../../managed-kubernetes/concepts/network-policy.md#cilium): an advanced plugin that uses advanced network policies applied [at the L7 layer (REST/HTTP, gRPC and Kafka)](https://docs.cilium.io/en/v1.10/gettingstarted/http/).

We recommend using the `default deny` rules for incoming and outgoing traffic by default with only the relevant traffic allowed.

To generate policies, you can use the Cilium CNI built-in Hubble platform to analyze the traffic manually. Various solutions for automatic generation of network policies are also available on the market.

For useful examples of network policies, see the [repo](https://github.com/ahmetb/kubernetes-network-policy-recipes).

A helpful tool to create both basic and advanced network policies is available [here](https://editor.cilium.io/).

#### Setting up incoming network access {#ingress}

For online endpoints, we recommend allocating an independent {{ k8s }} cluster or independent node groups (using such mechanisms as: [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/#:~:text=Node%20affinity%20is%20a%20property,onto%20nodes%20with%20matching%20taints) + [Node affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)). By doing this, you establish a DMZ so that if your nodes are compromised online, your attack surface is small.

To enable incoming network access to your workloads via HTTP/HTTPS, use the [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) resource.

There are at least two Ingress controller options that you can use in {{ yandex-cloud }}:
- [NGINX Ingress Controller](../../managed-kubernetes/tutorials/ingress-cert-manager.md).
- [{{ alb-name }} Ingress controller](../../application-load-balancer/tools/k8s-ingress-controller/index.md).

Benefits of {{ alb-name }} Ingress controller:
* Integration with the [{{ certificate-manager-full-name }}](../../certificate-manager/) cloud service.
* No need to install a controller to the cluster because everything is deployed on the [{{ alb-name }}](../../application-load-balancer/) side.

#### Restricting access to the metadata of VMs in the node group {#metadata-access-restriction}

For all pods, create a network policy to block network traffic to port 169.254.169.254 or use the default-deny policy from the [example](../../managed-kubernetes/operations/calico#enable-isolation). The policy must block workload node group metadata access because these node groups contain sensitive data, such as the token of the service account assigned to the node.

## {{ managed-k8s-name }} authentication and access management {#authentication-and-access-control}

The access of {{ iam-short-name }} accounts to {{ managed-k8s-name }} resources is managed at the following levels:

* [{{ managed-k8s-name }} service roles](../../managed-kubernetes/security/#yc-api) (access to the {{ yandex-cloud }} API): These enable you to control clusters and node groups (for example, create a cluster, create/edit/delete a node group, and so on).
* Service roles to access the {{ k8s }} API: These let you control cluster resources via the {{ k8s }} API (for example, perform standard actions with {{ k8s }}: create, delete, view namespaces, work with pods, deployments, create roles, and so on). Only the basic global roles are available at cluster level: `k8s.cluster-api.cluster-admin`, `k8s.cluster-api.editor`, and `k8s.cluster-api.viewer`.
* Primitive roles: These are global primitive {{ iam-short-name }} roles that include service roles (for example, the primitive role admin includes both the service administration role and the administrative role to access the {{ k8s }} API).
* Standard {{ k8s }} roles: Inside the {{ k8s }} cluster itself, you can use {{ k8s }} tools to create both regular roles and cluster roles. Thus you can control {{ iam-short-name }} accounts in terms of access at the namespace level. To assign {{ iam-short-name }} roles at the namespace level, you can manually create RoleBinding objects in a relevant namespace stating the cloud user's {{ iam-short-name }} ID in the **subjects name** field. Example:

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
    name: iam-user-aje0jndkhkvu04ek #object name RoleBinding
    namespace: micro1-ns 
    roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: ClusterRole
    name: admin
    subjects:
    - kind: User
    name: aje0jndkq855llvu04ek #cloud user ID
    ```

For the {{ managed-k8s-name }} cluster to run, you need two service accounts: [the service account of the cluster and the service account of the node group](../../managed-kubernetes/security/index.md#sa-annotation).

![](../../_assets/overview/solution-library-icon.svg)[Example of setting up role models and policies in {{ managed-k8s-name }}](https://github.com/yandex-cloud-examples/yc-mk8s-roles-and-policies).

## {{ managed-k8s-name }} secure configuration {#secure-config-1}

### Secure configuration {#secure-config-2}

In {{ managed-k8s-name }}, the user is fully in control of all node group settings, but only partially in control of the [master](../../managed-kubernetes/concepts/index.md#master) settings. These settings are part of the user's overall cluster security responsibility.

The [CIS {{ k8s }} Benchmark](https://www.cisecurity.org/benchmark/kubernetes) standard is designed to build a secure {{ k8s }} configuration, including node configurations.

In {{ yandex-cloud }}, the {{ k8s }} node groups are deployed by default with the configuration that complies with CIS {{ k8s }} Benchmark.

The [kube-bench](https://github.com/aquasecurity/kube-bench) tool enables you to check whether the node group configuration is compliant with CIS {{ k8s }} Benchmark. The tool officially supports the {{ yandex-cloud }} node groups.

[Here](https://github.com/aquasecurity/kube-bench/blob/main/docs/running.md) you can see the examples of launching kube-bench on the nodes.

In addition, kube-bench supports integration with [Starboard Operator](https://blog.aquasec.com/automate-kubernetes-compliance) — another product for kube-bench automatic launching.

Starboard Operator is a free tool that helps you automate scanning of images for vulnerabilities and checking that the configuration complies with CIS {{ k8s }} Benchmark.

![](../../_assets/overview/solution-library-icon.svg) [Integration between Starboard and {{ container-registry-full-name }} to scan running images](https://github.com/yandex-cloud-examples/yc-cr-trivy-integration)

### Integrity control (FIM — File integrity monitoring) {#fim}

You must control two levels of file integrity in node groups:

* Node OS files, e.g., configuration files.
* Container files, e.g., critical files the user application writes to the [volume](../../managed-kubernetes/concepts/volume.md).

#### Node OS files {#fim-OS-files}

You can use, for example, [Osquery](https://osquery.io/) as an agent installed on the nodes using [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) and uses specific node folders mounted as a volume into the DaemonSet container (redirected file system).

![](../../_assets/overview/solution-library-icon.svg)A comprehensive solution in [Osquery and kubequery in K8s.](https://github.com/yandex-cloud-examples/yc-mk8s-osquery-kubequery)

#### Container files {#fim-container-files}

One of the methods to solve this task:

1. Use [readOnlyRootFilesystem](https://www.thorsten-hans.com/read-only-filesystems-in-docker-and-kubernetes/) in pods.
2. Make sure to mount folders to write the data to as separate volumes: as emptydir or individual disks.

If you mount folders as emptydir, files are stored on the node in the `/var/lib/kubelet/pods/PODUID/volumes/kubernetes.ioempty-dir/VOLUMENAME` folder. To ensure data integrity, you can monitor this folder by Osquery as [node OS files](#fim-OS-files).

In the case of separate disks (not emptydir), you can mount volumes in read mode to the above-mentioned DaemonSet running Osquery.

To control file integrity on the {{ k8s }} nodes, you can also use the tools listed in [Integrity control](../standard/virtualenv-safe-config.md#integrity-control).

There exist dedicated free solutions for {{ k8s }} nodes from Google or Argus, including [file-integrity-operator](https://github.com/openshift/file-integrity-operator).

## Data encryption and {{ managed-k8s-name }} secret management {#encryption-and-secret-management}

At the {{ k8s }} etcd level, encrypt secrets using an in-built [mechanism from {{ yandex-cloud }}](../../managed-kubernetes/concepts/encryption.md).

We recommend that you use SecretManager solutions to work with {{ k8s }} secrets. [{{ lockbox-name }}](../../lockbox/) is such a solution in {{ yandex-cloud }}.

 {{ lockbox-name }} was integrated with {{ k8s }} using the [External Secrets](https://external-secrets.io/latest/) open-source project. In {{ marketplace-name }}, the solution is available in the basic simplified scenario: [External Secrets Operator with Yandex Lockbox support](/marketplace/products/yc/external-secrets).


Useful instructions on working with External Secrets:

* [Guide](https://external-secrets.io/latest/provider/yandex-lockbox/) on External Secrets and {{ lockbox-name }} from the project description.
* [Guide](../../lockbox/tutorials/kubernetes-lockbox-secrets.md) on External Secrets and {{ lockbox-name }} from the {{ yandex-cloud }} documentation.


Many methods to differentiate access to secrets using this tool have been [described](https://external-secrets.io/latest/guides/multi-tenancy/#eso-as-a-service).

The most secure recommended option for encrypting secrets is ESO as a Service (External Secrets Operator as a service). In this case, the global administrator has access to the namespace where ESO is installed, and administrators of individual namespaces create their own [`SecretStore`](https://external-secrets.io/latest/api/secretstore/) objects (where they specify {{ iam-short-name }} authorized access keys for their {{ lockbox-short-name }} secrets). If this `SecretStore` object is compromised, the authorized key of only one namespace will be compromised (not all of them, as in the case of Shared ClusterSecretStore).

### Encryption in transit {#encryption-in-transist}

For in-transit encryption, use TLS interaction between pods. If TLS cannot be used, use the service mesh solutions:

* [Istio](https://istio.io/)
* [Linkerd](https://linkerd.io/)

### Encryption at rest {#encryption-at-rest}

If you need to encrypt your stored data, you can use:

* {{ kms-name }}, for encrypting data at the application level (including when you use persistent volumes).
* A custom method of data encryption. However, in this case, protection of the keys and of the key management procedure is the sole responsibility of the user.

## Protection against malicious code in {{ managed-k8s-name }} {#malware-protection}

There are two levels where you can enable malicious code protection in {{ k8s }}:

* Container Registry level protection.
* OS-level protection of {{ k8s }} nodes.

Security scanner in [{{ container-registry-name }}](../../container-registry/concepts/vulnerability-scanner.md).

To protect the containerization host levels, you can use a variety of paid and free solutions from the "Runtime security" and "Antivirus engine" classes. Examples of free solutions:

* [{{ k8s }} ClamAV](https://cloud.google.com/community/tutorials/gcp-cos-clamav)
* [Sysdig Falco](https://falco.org/) (it can also function as an Intrusion Detection System)

Be sure to also use the {{ k8s }} built-in support for [AppArmor](https://kubernetes.io/docs/tutorials/security/apparmor/) and [Seccomp](https://kubernetes.io/docs/tutorials/security/seccomp/).

![](../../_assets/overview/solution-library-icon.svg)[Analyzing {{ k8s }} security logs in ELK: audit logs, Policy Engine, Falco.](https://github.com/yandex-cloud-examples/yc-export-mk8s-auditlogs-to-elk)

## Managing {{ managed-k8s-name }} vulnerabilities {#vulnerability-management}

{{ yandex-cloud }} within {{ managed-k8s-name }} is in charge of vulnerability management and security updates on the [master](../../managed-kubernetes/concepts/index.md#master). The user must independently control vulnerabilities on the {{ k8s }} worker nodes.

### Scanning for vulnerabilities {#vulnerability-scanning}

You can break vulnerability scanning into the following levels:

* Image-level vulnerability scanning.
* Vulnerability scanning of {{ k8s }} node OSes.

Vulnerability scanning at the image level is detailed in [Protection against malicious code in {{ managed-k8s-name }}](#malware-protection).

Examples of free universal solutions for node OS vulnerability scanning in {{ k8s }} are given in [{#T}](../standard/kubernetes-security.md).

There are also paid and free specialized solutions for scanning {{ k8s }} node OSes and {{ k8s }} hosts for vulnerabilities, e.g., the free kube-hunter and trivi (scan filesystem).

## Security updates {#security-updates}

{{ managed-k8s-name }} issues updates in a regular manner. To meet the Information Security standards:

* Select a relevant update channel and enable either automatic installation of updates, or manual installation immediately after publication in the selected channel.
* Check that the update settings meet the Information Security standards.
* Use one of the three latest {{ k8s }} versions, because updates (including security updates) are only released for these versions.

## Backup and recovery {#backup-and-restore}

Set up backups in {{ managed-k8s-name }} by following the [guide](../../managed-kubernetes/tutorials/kubernetes-backup.md). When storing your backups in {{ objstorage-name }}, follow recommendations from the [Secure configuration for {{ objstorage-name }}](../standard/virtualenv-safe-config.md#objstorage).

## Security policies in {{ k8s }} {#kubernetes-security-policies}

Requirements listed in [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/) from {{ k8s }} let you prevent threats related to {{ k8s }} objects.

To implement the requirements, you can either use the built-in {{ k8s }} [Pod Security Admission Controller](https://kubernetes.io/docs/setup/best-practices/enforcing-pod-security-standards/) tool or open-source software, e.g., other admission controllers: OPA Gatekeeper, [Kyverno](/marketplace/products/yc/kyverno).

Examples using Kyverno:

* ![](../../_assets/overview/solution-library-icon.svg)[Analyzing {{ k8s }} security logs in ELK: audit logs, Policy Engine, Falco.](https://github.com/yandex-cloud-examples/yc-export-mk8s-auditlogs-to-elk)
* ![](../../_assets/overview/solution-library-icon.svg)[Example of setting up role models and policies in {{ managed-k8s-name }}.](https://github.com/yandex-cloud-examples/yc-mk8s-roles-and-policies)

To control compliance with Pod Security Standards, you can also use the following tools within CI/CD:

* [Kyverno CLI](https://kyverno.io/docs/kyverno-cli/)
* [The gator CLI](https://open-policy-agent.github.io/gatekeeper/website/docs/gator)

Or a separate [Kubesec](https://kubesec.io/) tool.

## Secure practices of creating and using a Docker image {#best-practices-docker-image}

Use these check lists to meet requirements for secure creation of images:

* [Dockerfile best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
* [{{ k8s }} Security Checklist and Requirements](https://github.com/Vinum-Security/kubernetes-security-checklist/blob/main/README.md)

You can control Dockerfile in your CI/CD pipeline using the [Conftest](https://www.conftest.dev/) utility.

## Runtime protection {#runtime-protection}

When using minimal images or distroless images without a shell, use [ephemeral containers](https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/).

![](../../_assets/overview/solution-library-icon.svg)[Solution running Osquery.](https://github.com/yandex-cloud-examples/yc-mk8s-osquery-kubequery)

## Load sharing between nodes {#load-sharing}

Data loads with different security contexts (such as different severities of data processed) must be processed on different {{ k8s }} nodes. To enable load sharing within a cluster, use different node groups with different settings for [`node labels`](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes/) and [`node taints`](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). Be sure to combine both settings.

## Collecting, monitoring, and analyzing {{ managed-k8s-name }} audit logs {#collection-monitoring-analysis-audit-logs}

Events available to the user in the {{ managed-k8s-name }} service can be classified as levels:

* {{ k8s }} API events ({{ k8s }} audit logging)
* {{ k8s }} node events
* {{ k8s }} pod events
* {{ k8s }} metrics
* {{ k8s }} flow logs

### {{ k8s }} API level ({{ k8s }} audit logging) {#kubernetes-api-level}

Audit events are collected from the {{ k8s }} API level by {{ cloud-logging-name }}.

![](../../_assets/overview/solution-library-icon.svg)[Analyzing {{ k8s }} security logs in ELK: audit logs, Policy Engine, Falco.](https://github.com/yandex-cloud-examples/yc-export-mk8s-auditlogs-to-elk)

### {{ k8s }} node level {#kubernetes-nodes-level}

{{ k8s }} node level events are collected and exported similarly to [collecting OS audit logs](../standard/audit-logs.md#os-level).

### {{ k8s }} pod level {#kubernetes-pods-level}

To learn more about options for collecting and exporting pod-level events in {{ k8s }}, refer to the [{{ k8s }} official documentation](https://kubernetes.io/docs/concepts/cluster-administration/logging/).

Examples of collecting and exporting pod logs:

* Exporting logs to {{ cloud-logging-name }} using Fluent Bit is described in the [{{ managed-k8s-name }} documentation](../../managed-kubernetes/tutorials/fluent-bit-logging.md).
* Exporting pod logs into Elastic or Splunk is described in the [{{ yandex-cloud }} Security Solution Library](https://github.com/yandex-cloud-examples/yc-mk8s-osquery-kubequery/blob/main/README-en.md).

The [Filebeat](/marketplace/products/yc/filebeat) plugin for transferring logs to Elastic and [Fluent Bit with a {{ cloud-logging-name }} plugin](/marketplace/products/yc/fluent-bit) is available in {{ marketplace-name }}.

### {{ k8s }} metrics {#kubernetes-metrics}

{{ monitoring-name }} includes a set of metrics to analyze availability of {{ k8s }} objects and their behavioral anomalies.

For a guide on exporting {{ monitoring-name }} metrics, see the [Exporting events to SIEM](../standard/audit-logs.md#events) section.

### {{ k8s }} flow logs {#flow-logs-kubernetes}

![](../../_assets/overview/solution-library-icon.svg)[Exporting flow logs to {{ objstorage-full-name }}.](https://github.com/yandex-cloud-examples/yc-export-mk8s-cilium-logs-to-s3)

### {{ managed-k8s-name }} role model audit {#role-model-audit}

In the {{ managed-k8s-name }} console, you can audit the current role model used in the service. For this, go to the **Access management** tab in the service.

You can also use:
* [KubiScan](https://github.com/cyberark/KubiScan)
* [Krane](https://github.com/appvia/krane)
