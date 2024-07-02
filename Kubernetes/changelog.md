# Kubernetes 1.25 -> 1.26

- Starting from 1.25 container registry was changed from `k8s.gcr.io` to `registry.k8s.io`. this new registry spreads the load across multiple cloud providers & regions functioning as sort of CDN.
- If you run in a restricted environment and apply strict domaion/ip address access policies limited to k8s.gcr.io, the image pulls will not function after the migration to this new registry.

## Prerequisites to upgrade:

- VPC container network interface plugin (VPC) (CNI): You must upgrade VPC CNI plugin version to 1.12 or higher. earlier versions of VPC CNI will cause the CNI to crash, because it is relied on v1alpha2API, which has been removed from kubernetes 1.26.
- You must upgrade your containerd runtime to `version 1.6` or higher. K8S has dropped support for CRI v1alpha2, which causes the kubelet to not register nodes when the container runtime does not support CRI v1.
- In EKS 1.26, the AWS specific logic in kubelet has been disabled. If you used the EKS optimized AMI prior to 1.26, the kubelet was configured to use the in-tree cloud provider.

## Retired API versions and features:

- `apiserver.k8s.io/v1beta1` API Versions: among the api versions that were remove in 1.26 is the `flowcontrol.apiserver.k8s.io/v1beta1` API, which was marked as deprecated in v1.23. If you are using v1beta1 api versions of `FlowSchema` and `PriorityLevelConfiguration` resources, update k8s manifests and api clients to use the newer api version instead.
- **IMPORTANT**: among the API versions that were removed in K8S is the `autoscaling/v2beta2` API, which was marked as deprecated in v1.23. If you are currently using this API versions you must use the `autoscaling/V2API` version instead.
- `Dynamic kubelet configuration`: Dynamic kubelet configuration was removed v1.26 due to stability and security concerns. Previously, this configuration allowed you to roll out new kubelet configurations via the K8S API by specifying a configmap containing data that the kubelet should use for each node.

## Amazon EKS add-on:

- VPC CNI plugin for kubernetes is deployed on each Amazon EC2 node in EKS cluster. the add on created ENI and attaches them to the EC2 node. the add also assigns IPV4 or IPV6 address from your vpc to each pod and service.
- A version of the add-on is deployed with each Fargate node in your cluster. The version of the add-on for EKS v1.26 is `v1.18.0-eksbuild.1`

## add-ons

- Add-on is software that provides supporting operatuinal capabilities to K8S applications, but it not specific to the application
- Amazon EKS automatically installs self managed add-ons such as the amazon VPC CNI plugin for kubernetes, kube-proxy and coredns for every cluster
- Amazon EKS add-on allow you to ensure that your cluster is secure and stable and reduce the amount of that work you need to do to install, configure and update these add-ons.
- if EKS is created through `eksctl` with a config file or through `Amazon console` then `kube-proxy` and `Amazon VPC CNI` plugin and `coreDNS` are installed as add-ons. if cluster is created with ekstctl without config file or with any other tool, the self-managed versions of add-ons are installed
- if you are creating an add-on that uses a kubernetes service account and IAM role, then you need to have an AWS IAM OpenID Connect (OIDC) provider for your cluster.
  1. Determine the OIDC issuer ID for your cluster, Retrieve Cluster OIDC issuer ID and store in a variable
     ```shell
     oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)
     ```
  2. Determine whether an IAM OIDC provider with your cluster's issuer ID is already in your account:
  ```shell
      aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4
  ```
  If an output is returned, then you already have an IAM OIDC provider for your cluster
- **Updating add-on**:
  - Amazon EKS doesn't automatically update an add-on when new versions are released or after you update your cluster to a new kubernetes minor version.
  - To update an add-on for an existing cluster you must initiate the update.
  - Retrieve addon version compatibility:
  ```shell
      aws eks describe-addon-versions --addon-name amazon-cloudwatch-observability --kubernetes-version 1.29
  ```

# Kubernetes 1.26 -> 1.27:

- the old registry `k8s.gcr.io` is frozen and no further images for kubernetes and related sub-projects will be published to the old registry.

- `Seccomp` is a Linux Kernel feature, it can be used to restrict actions available within a container. The default seccomp profile provides a sane default for running containers with Seccomp and disables around 44 system calls out of 300+.
- `SeccompDefault` graduates to stable: If enabled then kubelet will use the RuntimeDefault seccomp profile by default, which is defined by the container runtime, instead of using the unconfined mode.
- In v1.22 K8S introduced a new accessMode `ReadWriteOncePod` for persistent volumes PVS and Persistent Volume Claims PVCS. This access mode enables you to restrict volume access to a single pod in the cluster, ensuring that only one pod can write to a volume at a time.

# Kubernetes 1.27 -> 1.28:

- Skew between core node and control plane components are expanded from n-2 to n-3. Which means node components (kube-proxy and kubelet) for the oldest supported minor version work with controlplane components (kube-apiserver, kube-scheduler, kube-controller-manager and cloud-controller-manager) for the newest supported minor version.
- with this we have the option to make minor version upgrade to nodes just once a week in each calendar year and still stay with upstream support.
- If a node shuts down unexpectedly or end up in a non recoverable state (due to hardware failure), K8S allows you to clean up afterward and allow stateful workloads to restart on a different node.
- In 1.28, two optional fields reason and fieldPath were added to allow user to specify the failure reason and fieldPath when validation failed.
-
