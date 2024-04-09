# Kubernetes Attack Surface:
- **4C's of Cloud Native Security**: 
    - `Cloud`: The cloud itself can be attacked when it enables access to cluster from anywhere. If firewalls where in place then we would have blocked access.
    - `Cluster`: The attacker gained access to the cluster through the docker daemon that was exposed publically withouth authentication and authorization mechanisms. 
    - `Container`: The attacker was able to run a priviledged container which should have been prevented. The attacker was also able to install utilities like curl.
    - `Code`: Code refers to the application code itself. Hardcoding credentials to access database or using environment variables to store them is a security vulnerability or exposing application without TLS are all bad coding practices.
    ![4cs](./../assets/Kubernetes/4c.png)

## CIS Benchmarks:
- **Security Benchmark**:  
![alt text](./../assets/Kubernetes/security_benchmarks.png)
    - There are a lot of tools to asses our servers and one of them is `CIS Center for Internet Security` and it is a community driven non profit organization.
    - `CIS-CAT Lite`: this tools compares the current configuration of the cluster against the best practices and publishes a report in HTML format. 
    - `Kube-bench` of `Aqua Securityx   `