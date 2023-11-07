# Service Mesh

instead of having all different requirements (Authentication, Authorization, Networking, Logging, Monitoring, Tracing) in each microservice we replace with a single proxy in the form a sidecar container.

![Linux Bridge](./../assets/istio/intro.png)

- A Service Mesh is a dedicated and configurable infrastructure layer that handles the communication between services without having to change the code in a microservice architecture.
- a service mesh offers: **Traffic Management**, **Security**, **Obersvability**, and **Service Discovery** (Health Checks, Load Balancing and Discovery)

# Istio

- Free and open source service mesh that offers a secure way to connect and monitor services.

![Alt text](./../assets/istio/architecture.png)

- **Citadel**: Manages certificate generation
- **Pilot**: Help with service discovery
- **Galley**: help validating configuration
- all these three were combined into a single daemon called **istiod**
- Each service has another component called **Istio Agent** responsible for passing configuration secrets to the envoy proxy

- In order to allow Istio to automatically add the sidecar container envoy for each pod we need to add the labed `istio-injection=enabled` to that namespace.

  `kubectl label namespace default istio-injection=enabled`
