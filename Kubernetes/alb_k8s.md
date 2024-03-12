# ALB on Amazon EKS
- Verify that AWS Load Balancer Controller is installed by following this [Documentation](https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html). The version of the controller should be 2.5.4 or later.
- Verify that we have two subnets in different AZs.
- **Private Subnets**: must be tagged following a specific format. This is used is so k8s and ALB know that the subnet can be used for internal load balancers:
    - `key`: kubernetes.io/role/internal-elb
    - `value`: 1
- **Public Subnets**: must be tagged following a specific format. This is so K8S knows to only use the subnets that were specified for external load balancers.
    - `key`: kubernetes.io/role/elb
    - `value`: 1
- The AWS Load Balancer controller creates ALB and the necessary supporting AWS resources whenever a K8S ingress resource is created on the cluster with the `kubernetes.io/ingress.class: alb` annotation.
- When this annotation is added then the field `spec.ingressClassName` should be removed.
- In order to tag the ALB created by AWS Load balancer controller add the following annotation `alb.ingress.kubernetes.io/tags: x`
- In order to lower the cost we can share the same ALB with multiple K8S ingress rules. In order to share the same ALB for a team or any combination of apps specify the following annotation `alb.ingress.kubernetes.io/group.name`. All services with the same group name will use the same LoadBalancer.
- To create an ALB that is shared by different ingresses across namespaces, we have to add the same `alb.ingress.kubernetes.io/group.name: x-name` tag for all ingress resources created.
- The ALB performs health checks on the targets in its target group. If all targets are failing health checks, the ALB will return a 503 error.

### References:
- [Share Same ALB](https://medium.com/@philipdam8/using-a-single-alb-ingress-across-multiple-kubernetes-namespaces-556df9b51f80)
