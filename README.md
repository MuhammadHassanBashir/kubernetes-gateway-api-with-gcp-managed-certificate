*Title: Implementing Kubernetes API Gateway with TLS Certificate in Google Cloud Platform*

**Introduction**:
Kubernetes API Gateway plays a pivotal role in managing and routing traffic to microservices deployed within Kubernetes clusters. It serves as the central entry point for external traffic and provides functionalities such as routing, load balancing, and security enforcement. Securing the communication between clients and the API gateway is crucial, and this is achieved by configuring a TLS certificate.

**Creating TLS Certificate in Google Cloud Platform (GCP)**:
To create a TLS certificate in GCP for use with the Kubernetes API Gateway, follow these steps:

**DNS Authorization Creation**:

  gcloud certificate-manager dns-authorizations create auth-test \
      --domain="ttest.disearch.ai"

This command creates a DNS authorization for the specified domain.

**Check DNS Authorization Details**:

  gcloud certificate-manager dns-authorizations describe auth-test

Use this command to check the CNAME for authorization, which will be required later.

**Certificate Creation**:

  gcloud certificate-manager certificates create gateway-test \
    --domains=ttest.disearch.ai --dns-authorizations=auth-test

Create a TLS certificate named "gateway-test" for the domain "ttest.disearch.ai".

**Map and Map Entries Creation**:

  gcloud certificate-manager maps create gateway-test

  gcloud certificate-manager maps entries create gateway-test \
      --map="gateway-test" \
      --certificates="gateway-test" \
      --hostname="ttest.disearch.ai"

Create maps and map entries to associate the certificate with the domain.

**Update DNS Records**:
Update the DNS records in GCP Cloud DNS with the CNAME and DATA details obtained earlier. 

  gcloud certificate-manager dns-authorizations describe auth-test

You can get record with this command and add it to cloud dns accordingly.

**Attach Certificate to Gateway**:
Update the Gateway resource YAML file with the annotation specifying the certificate name and the hostname.

yaml
metadata:
  annotations:
    networking.gke.io/certmap: gateway-test
spec:
  listeners:
  - name: https
    hostname: "*.disearch.ai"

**Deployment**:
Apply the updated deployment files, including the Deployment, Service, HTTPRoute, Gateway, and ReferenceGrant.

Testing:
After completing the deployment, browse to the configured domain in a web browser. The browser should receive a valid TLS certificate from the CA, indicating that the communication with the Kubernetes API Gateway is secure.

By following these steps, you can securely configure and deploy Kubernetes API Gateway with TLS certificate encryption in Google Cloud Platform.



