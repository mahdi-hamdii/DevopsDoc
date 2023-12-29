# Kubernetes Autoscaler:

- Cluster autoscaler is a tool that automatically adjusts the size of kubernetes cluster when:
  - There are pods in the cluster that failed to run in cluster due to insufficient resources.
  - There are nodes in the cluster that have been underutilized for an extended period of time and their pods can be placed on other existing nodes.

## OIDC Federated Authentication:

- OIDC federated authentication allows your service to assume an IAM role and interact with aws service without having to store credentials as environment variables.

1. Create IAM OIDC identity provider for EKS cluster
2. Create IAM role for the service account in the console:
   - retrieve OIDC issuer URL from the amazon EKS. `https://oidc.eks.us-east-1.amazonaws.com/id/xxxxxxxxxx`
   - While creating a new IAM role we have to choose the `web identity` option in `type of trusted entity`
   - In Choose `web identity provider`. For identity provider, choose the url for the cluster. For audience type `sts.amazonaws.com`
   - attach policy

# Create EKS Cluster (This is done using the EKS cluster module)

- In this example we are going to use the eks module in order to create an EKS cluster.
- we are going to use managed node groups which will creates our autoscaling groups with the needed Tags
- Each autoscaling group must has those 2 tags for the Autoscaler to work properly:
  - `k8s.io/cluster-autoscaler/<cluster name>	owned	Yes`
  - `k8s.io/cluster-autoscaler/enabled	true	Yes`

# Create the OIDC Provider (This is done using the EKS cluster module)

- Copy the OIDC link from the EKS cluser configuration: `https://oidc.eks.REGION.amazonaws.com/id/XXXXXXXXXXXXXXXXXXXX`
- Go to IAM, Identity Providers, Add Provider, OIDC, for the value paste `sts.amazonaws.com`

# Create IAM Policy for auto scaler

- Go to Poicies, Create Policy with those permissions

```
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Action": [
              "autoscaling:DescribeAutoScalingGroups",
              "autoscaling:DescribeAutoScalingInstances",
              "autoscaling:DescribeLaunchConfigurations",
              "autoscaling:DescribeTags",
              "autoscaling:SetDesiredCapacity",
              "autoscaling:TerminateInstanceInAutoScalingGroup",
              "ec2:DescribeLaunchTemplateVersions"
          ],
          "Resource": "*",
          "Effect": "Allow"
      }
  ]
}
```

You can name the policy `AmazonEKSClusterAutoscalerPolicy`

# Create IAM Role with the Policy created

- Go to Roles, Create Role, `Web Identity`, select the OIDC Provider that we created for our cluster, and select the audience
- Select the Policy that we created `AmazonEKSClusterAutoscalerPolicy`
- Name this role `AmazonEKSClusterAutoscalerRole`

# Change the Role

- Go to the Role `AmazonEKSClusterAutoscalerRole` go to `Trust relationships`, click on `Edit trust policy` and update it
- The update should be like that:
  - From this: `"StringEquals": {
	"oidc.eks.us-east-2.amazonaws.com/id/XXXXXXXX:aud": "sts.amazonaws.com"
}`
  - To this: `"StringEquals": {
	"oidc.eks.us-east-2.amazonaws.com/id/XXXXXXXX:sub  ": "system:serviceaccount:kube-system:cluster-autoscaler"
}`

# Deploy the Auto scaler to the EKS Cluster

- Apply the manifest called 00-cluster-autoscaler.yaml

https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md

# Automating the stuff

## Create EKS Cluster - (This is done using the EKS cluster module)

## Create the OIDC Provider - (This is done using the EKS cluster module)

## Create IAM Policy for auto scaler

- Go to Poicies, Create Policy with those permissions

```
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Action": [
              "autoscaling:DescribeAutoScalingGroups",
              "autoscaling:DescribeAutoScalingInstances",
              "autoscaling:DescribeLaunchConfigurations",
              "autoscaling:DescribeTags",
              "autoscaling:SetDesiredCapacity",
              "autoscaling:TerminateInstanceInAutoScalingGroup",
              "ec2:DescribeLaunchTemplateVersions"
          ],
          "Resource": "*",
          "Effect": "Allow"
      }
  ]
}
```

You can name the policy `EKSClusterAutoscalerPolicy`

## Create IAM Role with the Policy created

- Go to Roles, Create Role, `Web Identity`, select the OIDC Provider that we created for our cluster, and select the audience
- Select the Policy that we created `AmazonEKSClusterAutoscalerPolicy`
- Name this role `AmazonEKSClusterAutoscalerRole`

## Change the Role

- Go to the Role `AmazonEKSClusterAutoscalerRole` go to `Trust relationships`, click on `Edit trust policy` and update it
- The update should be like that:
  - From this: `"StringEquals": {
	"oidc.eks.us-east-2.amazonaws.com/id/XXXXXXXX:aud": "sts.amazonaws.com"
}`
  - To this: `"StringEquals": {
	"oidc.eks.us-east-2.amazonaws.com/id/XXXXXXXX:sub  ": "system:serviceaccount:kube-system:cluster-autoscaler"
}`

## Thumbprint manual steps:

é
DOC: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc_verify-thumbprint.html#thumbstep2

oidc.eks.us-east-2.amazonaws.com/id/CE3E1BC238F08F6A7257A668F59C5120

https://oidc.eks.us-east-2.amazonaws.com/id/CE3E1BC238F08F6A7257A668F59C5120/.well-known/openid-configuration

oidc.eks.us-east-2.amazonaws.com

openssl s_client -servername oidc.eks.us-east-2.amazonaws.com -showcerts -connect oidc.eks.us-east-2.amazonaws.com:443

openssl x509 -in certificate.crt -fingerprint -sha1 -noout

# Solution2 use the kubergrunt CLI

https://github.com/gruntwork-io/kubergrunt

Kubergrunt is a standalone go binary with a collection of commands to fill in the gaps between Terraform, Helm, and Kubectl.
