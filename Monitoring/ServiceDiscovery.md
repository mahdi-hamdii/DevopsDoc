# Prometheus

- Prometheus follows a pull model which gives us many advantages in terms of scalability but implies that each service to be monitored needs to be registred on the supervising server(Prometheus).

- If the work environment is very dynamic especially in the case of kubernetes it becomes tough to manually add scrap configs. Hopefully, Prometheus offers multiple Service Discovery modules for different system componenets such as AWS, GCP, Kubernetes, OpenStack, etc.
- file_sd offers a generic way of implementing service discovery rather than adding new modules in their code.

- This is the service discovery list for prometheus:
> https://github.com/prometheus/prometheus/tree/main/discovery

# AWS Service Discovery Mechanism

- Create new IAM User with Programmatic Access.

- Attach existing policy of #AmazonEC2ReadOnlyAccess

- Tag the IAM user with service:prometheus. This step is optional, however when the number of services and IAM users grows in the futur we can keep track of users.

- Start a new ssh session to the prometheus instance

- edit the configuration file under /etc/prometheus/prometheus.yml
    ```
    global:
    scrape_interval: 1s
    evaluation_interval: 1s

    scrape_configs:
    - job_name: 'node'
      ec2_sd_configs:
          - region: us-east-2
            access_key: PUT_THE_ACCESS_KEY_HERE
            secret_key: PUT_THE_SECRET_KEY_HERE
            port: 9100
            filters:
              - name: tag:scrape
                values:
                  - true
              - name: tag:tier
                values:
                  - backend
    ```
- Modify Launch template to add instance tags for future instance
- modify the new launch template as the default one
- scale the ALB to it.
 
- Restart Prometheus Service.
```
sudo systemctl restart prometheus

```
