Ques) How cert manager works and how it helps in creating SSL certificate and attach them in Kubernetes?
Ans) Cert-Manager works by integrating tightly with Kubernetes to create, manage, and renew SSL/TLS certificates for your applications. Here’s a step-by-step overview of how it works:

1. Installation: First, you install Cert-Manager in your Kubernetes cluster. This typically involves applying Kubernetes manifests, which deploy the Cert-Manager controllers and CRDs (Custom Resource Definitions) into your cluster.

2. Certificate Requests:
Issuer: Cert-Manager uses Issuer or ClusterIssuer resources to define how certificates will be obtained. This can be configured for different types of Certificate Authorities (CAs), like Let's Encrypt, HashiCorp Vault, or an internal CA.
Certificate: Once an Issuer is set up, you can define Certificate resources that specify the required certificates for your services. The Certificate resource defines the desired properties of the certificate (e.g., domain name, DNS names, secret name to store the certificate, etc.).

3. Certificate Creation Process:
Cert-Manager checks the Issuer to figure out how to obtain the certificate (e.g., from Let’s Encrypt using HTTP-01 or DNS-01 challenges).
Cert-Manager then requests the certificate and automatically handles the challenge (e.g., proving domain ownership to the CA) and obtains the certificate from the CA.
Once the certificate is obtained, Cert-Manager stores it in a Kubernetes Secret resource, which is then used by Kubernetes workloads like Ingress controllers, services, etc.

4. Automatic Renewal: Cert-Manager automatically manages the renewal of certificates. When a certificate is nearing expiration, Cert-Manager will trigger a renewal process to request a new certificate, ensuring that your applications always have valid certificates without manual intervention.

Steps to Create and Attach SSL Certificates in Kubernetes with Cert-Manager:

1. Install Cert-Manager:
   To install Cert-Manager, you can use Helm or kubectl to apply the required manifests. Here's a typical installation process using Helm:

   
   kubectl create namespace cert-manager
   helm repo add jetstack https://charts.jetstack.io
   helm repo update
   helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.10.0
   

2. Create an Issuer or ClusterIssuer:
   Define an Issuer or ClusterIssuer resource that specifies how certificates will be issued. For example, if using Let’s Encrypt with HTTP-01 challenge:

   
   apiVersion: cert-manager.io/v1
   kind: Issuer
   metadata:
     name: letsencrypt-issuer
     namespace: default
   spec:
     acme:
       email: your-email@example.com
       server: https://acme-v02.api.letsencrypt.org/...
       privateKeySecretRef:
         name: letsencrypt-private-key
       solvers:
http01:
           ingress:
             class: nginx
   

   This issuer will use Let’s Encrypt to issue certificates and use HTTP-01 challenges via an Ingress.

3. Create a Certificate Resource:
   Define a Certificate resource that specifies the domains and other details about the SSL/TLS certificate you want to create. For example:

   
   apiVersion: cert-manager.io/v1
   kind: Certificate
   metadata:
     name: example-tls
     namespace: default
   spec:
     secretName: example-tls-secret
     issuerRef:
       name: letsencrypt-issuer
     dnsNames:
example.com
www.example.com
   

secretName refers to the Kubernetes Secret where the certificate will be stored.
dnsNames specifies the domain names the certificate should cover.

4. Attach the Certificate to an Ingress:
   Once the certificate is created and stored in a Secret, you can reference it in an Ingress resource to secure your application. For example, if you're using an NGINX Ingress Controller:

   
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: example-ingress
     namespace: default
     annotations:
       nginx.ingress.kubernetes.io/ssl-redirect: "true"
       nginx.ingress.kubernetes.io/secure-backends: "true"
       nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
   spec:
     rules:
host: example.com
       http:
         paths:
path: /
           pathType: Prefix
           backend:
             service:
               name: example-service
               port:
                 number: 80
     tls:
hosts:
example.com
       secretName: example-tls-secret
  

The tls block references the Secret (example-tls-secret) that contains the SSL/TLS certificate and private key, which is automatically created by Cert-Manager.

5. Automatic Renewal:
   Cert-Manager will handle the automatic renewal of the certificate. It will periodically check if the certificate is approaching its expiration and will initiate a renewal process automatically.
