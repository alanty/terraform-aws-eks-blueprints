# Kubecost

Kubecost provides real-time cost visibility and insights for teams using Kubernetes, helping you continuously reduce your cloud costs.
Amazon EKS supports Kubecost, which you can use to monitor your costs broken down by Kubernetes resources including pods, nodes, namespaces, and labels.
[Cost monitoring](https://docs.aws.amazon.com/eks/latest/userguide/cost-monitoring.html) docs provides steps to bootstrap Kubecost infrastructure on a EKS cluster using the Helm package manager.

For complete project documentation, please visit the [Kubecost documentation site](https://www.kubecost.com/).

Note: If your cluster is version 1.23 or later, you must have the [Amazon EBS CSI driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html) installed on your cluster.

## Usage

Kubecost can be deployed by enabling the add-on via the following.

```hcl
enable_kubecost = true
```

Deploy Kubecost with custom `values.yaml`

```hcl
  # Optional Map value; pass kubecost-values.yaml from consumer module
    kubecost_helm_config = {
    name       = "kubecost"                                             # (Required) Release name.
    repository = "oci://public.ecr.aws/kubecost"                        # (Optional) Repository URL where to locate the requested chart.
    chart      = "cost-analyzer"                                        # (Required) Chart name to be installed.
    version    = "1.96.0"                                               # (Optional) Specify the exact chart version to install. If this is not specified, it defaults to the version set within default_helm_config: https://github.com/aws-ia/terraform-aws-eks-blueprints/blob/main/modules/kubernetes-addons/kubecost/locals.tf
    namespace  = "kubecost"                                             # (Optional) The namespace to install the release into.
    values = [templatefile("${path.module}/kubecost-values.yaml", {})]
  }
```

### Existing node-exporter and kube-state-metrics
If you already have `node-exporter` or `kube-state-metrics` installed in your cluster you may run into conflicts as the kubecost helm chart will also try to install those components. In this case, [kubecost recommends disabling these components in the kubecost helm chart](https://docs.kubecost.com/install-and-configure/install/custom-prom#disable-node-exporter-and-kube-state-metrics-recommended). Kubecost will leverage the pre-existing metrics agents to gather resource usage details. 

Example helm_config to disable those components:

```hcl
enable_kubecost = true
kubecost_helm_config = {
  set = [
    {
      name  = "prometheus.nodeExporter.enabled",
      value = false,
    },
    {
      name  = "prometheus.serviceAccounts.nodeExporter.create",
      value = false
    },
    {
      name  = "prometheus.kubeStateMetrics.enabled",
      value = false
    },
  ]
}
```



### GitOps Configuration

The following properties are made available for use when managing the add-on via GitOps.

```sh
kubecost = {
  enable  = true
}
```
