AWX-Installation
This repo provides the details how to install to AWX in Kubernetes cluster & enable self signed certificate to nginx ingress controller.

Prerequisites
A Kubernetes Cluster.
A Kubectl CLI Tool.
Step 1: Clone the AWX GitHub repository
git clone https://github.com/becloudready/awx.git

cd awx

git checkout -b branch-17
Step 2: Edit & Delete the following files
Now go inside the installer directory
cd installer

Edit the following values in the inventory file.
kubernetes_context=Your context name  # You can find this in kubeconfig file

kubernetes_namespace=Your namespace   # You can give any name for namespace

kubernetes_web_svc_type=NodePort

admin_user=admin          # Default user name

admin_password=password   # Default password

Step 3: Run the Ansible Playbook
Now go to the directory awx/installer & run the ansible playbook install.yml by running the following command.
ansible-playbook -i inventory install.yml
It will take around 5-10 minutes to complete.
Step 4: Access the AWX Web UI
To access the AWX web interface with NodePort IP & NodePort port number run the following command
kubectl get nodes -o wide | awk {'print $1" " $2 " " $7'} | column -t

Now you can access the awx web interface at http://NodePort-IP:Port
Since its not good practice to access the site with NodePort IP & NodePort port number because whenever node fails & restarts its NodePort IP & NodePort port number changes, so end user will not be able to access the site.
To overcome this issue, we are going to install Nginx Ingress controller inside the Kubernetes Cluster. This Ingress controller will be having a static external ip with which we are able to access the site even if node fails & restart.
Step 5: Install Nginx Ingress Controller
Since, I am using Digital Ocean Cloud platform, I will run the following command to install Nginx ingress controller.
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/do/deploy.yaml

Next, we will create a ingress resource file awx-ingress.yml, so that we will be able to access the awx site with External IP of ingress controller.
Contents of awx-ingress.yml will be as shown below.
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: awx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
        - path: /
          backend:
            serviceName: awx-web-svc
            servicePort: 80
Create the Ingress resource by running the following command
kubectl apply -f awx-ingress.yml

Verify the IP address is set:
kubectl get ingress

Now copy the External IP of ingress controller and paste it in your browser, we will be able to access the awx site.
Step 6: Create TLS certificate
Let us create a self signed TLS certificate by running the following command.
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=awxsvc/O=awxsvc"

Create Kubernetes Secret
kubectl create secret tls tls-secret --key tls.key --cert tls.crt

Step 7: Use Certification Secret within Nginx Ingress Controller
We now need to add the secret to the previously created ingress resource file awx-ingress.yml as shown below.
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: awx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - secretName: tls-secret-nginx
  rules:
  - host: www.awx.com
    http:
      paths:
      - backend:
          serviceName: awx-web-svc
          servicePort: 80
Now,apply the secret by running the following command
kubectl apply -f awx-ingress.yml

Step 8: Access the Application via HTTPS
Accessing the application via HTTPS is simple: we just need to add ‚https://‘ in front of the domain:
curl -k https://DOMAIN_NAME

Thats It, awx site is reachable via HTTPS
