# Horizental Pod Autoscaling

- HPA does not apply to objects that can't be scaled like DaemonSet.
- HPA is implemented as Kubernetes API resource and a controller. The resource determines the behavior of the controller.
- Kubernetes implements horizental pod autoscaling as a control loop that runs intermittently (it is not a continious process). The intervall is set by the `--horizontal-pod-autoscaler-sync-period` parameter to the kube-controller-manager and the default interval is 15 seconds.

- The common use of HPA is to configure it to fetch metrics from aggregated APIs (`metrics.k8s.io`, `custom.metrics.k8s.io`, or `external.metrics.k8s.io`). The metrics.k8s.io API is usually provided by an add-on named Metrics Server, which needs to be launched separately.
- When defining the pod specification the `resource requests` like `cpu` and `memory` should be specified. This is used to determine the resource utilization and used by the HPA controller to scale the target up or down.

```YAML
type: Resource
resource:
  name: cpu
  target:
    type: Utilization
    averageUtilization: 60
```

- HPA also supports a container metric source where the HPA can track the resource usage of individual containers across a set of pods, in order to scale the target resource. This feature was added starting from `Kubernetes 1.27`

```YAML
type: ContainerResource
containerResource:
  name: cpu
  container: application
  target:
    type: Utilization
    averageUtilization: 60
```

- If you use V2 HPA API, you can use the `behavior` field to configure separate scale-up and scale-down behaviors. You specify these behaviours by setting `scaleUp` or `scaleDown` under the behavior field.
- You can specify a `stabilization window` that prevents `flapping` (reffered to as `trashing` or `fluctuating`) the replica count for a scaling target.
- one or more scaling policies can be specified in the behavior section of the spec. When multiple policies are specified the policy which allows the highest amount of change is the policy which is selected by default.

```YAML
behavior:
  scaleDown:
    policies:
    - type: Pods
      value: 4 #allows at most 4 pods to be scaled down in one minute
      periodSeconds: 60
    - type: Percent
      value: 10 # allows at most 10% of the current replicas to be scaled down in one minute
      periodSeconds: 60
```

- `periodSeconds` indicates the length of time in the past for which the policy must hold true. The maximum value that you can set for periodSeconds is 1800 (half an hour).
- by default, the policy which allows the highest amount of change is selected, then the first policy will be applied (unless we have than 40 replica)
- `Stabilization window` is used to restrict the flapping of replica count when the metrics used for scaling keep fluctuating.

```YAML
behavior:
  scaleDown:
    stabilizationWindowSeconds: 300

```

- HPA is supported using `kubectl`. you can create a new autoscaler using `kubectl create hpa` command. for instance, executing `kubectl autoscale rs foo --min=2 --max=5 --cpu-percent=80` will create an autoscaler for replicaset foo, with target CPU utilization set to 80% and the number of replicas between 2 and 5.
- When HPA is enabled, it is recomended that the value of `spec.replicas` of the deployment or statefulset be removed from their manifests. if spec.replicas exist each time kubectl apply is done the deployment will be scaled to number specified in the manifest.

# Metrics Server EKS

- The kubernetes metrics server is an aggregator of resource usage data in your cluster, and it isn't deployed by default in EKS clusters. Metrics server is commonly used by `HPA` or `kubernetes Dashboard`.

`The metrics are meant for point-in-time analysis and aren't an accurate source for historical analysis. They can't be used as a monitoring solution or for other non-auto scaling purposes`
