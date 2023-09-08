# Auditing of the Kubernetes Control Plane in a Cloud Environment (AWS)

In today's security landscape, constant monitoring and alerting of suspicious activities within a Kubernetes cluster is not just a necessity but a mandate. To this end, enabling auditing on the control plane of an **AWS Elastic Kubernetes Service (EKS)** cluster forms a pivotal component. This setup creates a CloudWatch Logs log group wherein auditing events from the EKS control plane are chronologically logged, forming a reliable and detailed account of interactions with the cluster's API server.

An initiative was undertaken to evaluate these logs against Falco's well-defined audit rules. Falco brings to the table a specialized plugin called `k8saudit-eks`, which is adept at reading and analyzing Kubernetes audit events sourced from AWS EKS clusters. This plugin operates by sourcing data directly from the CloudWatch logs, thus offering real-time insights and alerts.

To facilitate this, several preparatory steps were required:

1. **Plugin Deployment**:
   The initial step involved deploying the `k8saudit-eks` plugin, a process that warranted a multi-faceted approach. Unfortunately, at the time, none of the official Falco images available on DockerHub had this plugin pre-installed. Furthermore, a dynamic library for it wasn't compiled either. However, the necessary codebase for the plugin was readily available on [Falco's GitHub repository](https://github.com/falcosecurity/plugins/tree/master/plugins/k8saudit-eks).

2. **Role and Permission Configuration**:
   Integration with AWS services was facilitated using OpenID Connect, granting the necessary permissions to access these services from within the EKS cluster. This procedure entailed annotating the Kubernetes Falco service account with the ARN of an AWS IAM role. This role contained permissions to read the EKS control plane CloudWatch log group logs, forming a bridge for the seamless transfer of log data to the Falco Kubernetes deployment.

3. **Container Image Configuration**:
   To utilize an AWS IAM role successfully, the container image needs to house the `ca-certs` package. Its absence results in an impediment when assuming the AWS role, manifesting in the error message `x509: certificate signed by unknown authority`.

4. **Visualization and Analysis**:
   The Falco sidecick and its web-UI were enabled in the values passed to the helm charts and the evaluated results from the Falco audit rules were visually displayed. This offeried a streamlined method to monitor and assess potential security threats.

With this setup in place, the AWS EKS environment gets augmented with an extra layer of security, monitoring and alerting on potential suspicious activities within the cluster, thereby fortifying its defenses against potential threats.

During this initiative was observed that Falco processed all logs from the cloudwatch log group, not only new events at the time of the helm installation, but also logs that were created from the EKS control plane logging being enabled in the past.


