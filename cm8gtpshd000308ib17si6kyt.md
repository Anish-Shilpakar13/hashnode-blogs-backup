---
title: "Deploying Kubernetes on AWS EC2: Infrastructure Management Made Easy for Data Engineers"
seoTitle: "Deploying Kubernetes on AWS EC2: Simplifying Infrastructure Management"
seoDescription: "Deploy Kubernetes on AWS EC2 for scalable data engineering. Learn setup, network, and deploy a "Hello World" app to verify. Ideal for ETL, and analytics."
datePublished: Wed Oct 30 2024 18:15:00 GMT+0000 (Coordinated Universal Time)
cuid: cm8gtpshd000308ib17si6kyt
slug: deploying-kubernetes-on-aws-ec2-infrastructure-management-made-easy-for-data-engineers
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742442508994/3f6be2a1-669a-46aa-b533-5b455fb6012c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1742442913643/972dba7b-1ab1-43fe-bd8a-d0a72c6c76b9.png
tags: ec2, linux, aws, kubernetes, data-engineering

---

# Introduction

In data engineering, efficient infrastructure management is as crucial as building robust data pipelines. As data volumes grow, scalability and resilience become essential for seamless processing and analytics. **Kubernetes** simplifies this by automating deployment, scaling, and management of containerized applications, ensuring efficient resource use and fault tolerance. While **AWS EKS** provides a fully managed Kubernetes service, **AWS EC2** offers a flexible, scalable environment for setting up Kubernetes clusters on virtual machines, optimizing performance and cost. In this blog, we’ll explore how to deploy Kubernetes on AWS EC2 to streamline infrastructure management for data engineering.

# About Kubernetes

Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the **Cloud Native Computing Foundation (CNCF)**. Kubernetes provides a unified way to manage infrastructure, ensuring that applications run reliably across distributed environments. For data engineers, infrastructure management often involves provisioning and maintaining resources to support ETL pipelines, data lakes, real-time streaming, and machine learning workloads. Kubernetes streamlines this process in several ways:

* **Automated Deployment & Scaling** – Kubernetes automatically manages the deployment of containerized workloads and scales them based on demand. This ensures efficient resource utilization, whether handling small data transformations or large-scale batch processing.
    
* **High Availability & Fault Tolerance** – Kubernetes ensures system resilience by distributing workloads across multiple nodes. If a container fails, Kubernetes automatically restarts it or redistributes the workload, preventing data pipeline failures.
    
* **Resource Optimization** – Kubernetes allows engineers to define CPU and memory limits, ensuring that applications don’t consume excessive resources. This is particularly useful when running multiple workloads on shared infrastructure.
    
* **Simplified Multi-Cloud & Hybrid Deployments** – Kubernetes abstracts away underlying infrastructure, enabling seamless deployment across on-premises, AWS, Google Cloud, or Azure environments. This flexibility is crucial for enterprises with hybrid or multi-cloud strategies.
    
* **Efficient Job Scheduling** – Kubernetes provides job scheduling capabilities for batch workloads, ensuring that ETL or AI/ML pipelines run at optimal times with the necessary resources.
    
* **Service Discovery & Load Balancing** – Kubernetes automatically manages networking between services, making it easier to build and maintain distributed data processing applications.
    

Kubernetes has become a cornerstone of modern data engineering due to its ability to provide a consistent, scalable, and automated infrastructure. Its popularity stems from industry adoption by major cloud providers like AWS, support for big data and AI workloads, cost efficiency, and open-source support.

# About AWS EC2

Amazon Elastic Compute Cloud (**AWS EC2**) is a **scalable cloud computing service** that provides virtual servers, known as **instances**, to run applications. It allows users to **deploy, configure, and manage** virtual machines with flexible CPU, memory, storage, and networking options. EC2 supports **auto-scaling, load balancing, and on-demand provisioning**, making it ideal for hosting applications, databases, and services like Kubernetes. By leveraging **Kubernetes on AWS EC2**, data engineers can deploy and manage their workloads efficiently, ensuring scalability, reliability, and automation—all critical factors in modern data-driven applications. In the next sections, we’ll explore how to set up a Kubernetes cluster on AWS EC2 to optimize data engineering workflows.

# Configuration Steps

To configure the Kubernetes cluster in an AWS Ec2 instance, the following steps are involved.

1. **Navigate to AWS EC2**
    
    Open AWS Management Console, then search for EC2 and navigate to the EC2 dashboard
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742432733359/ad9cc54b-f930-4586-8c08-e4ec6c8f8ee0.png align="center")
    
2. **Launch a New EC2 instance**
    
    Click on **Launch Instance** to create a new EC2 instance.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742432845679/805e2012-db92-4bb4-bb2f-0b54a9122b38.png align="center")
    
3. **Configuration of EC2 instance**
    
    Provide a suitable **name** to the EC2 instance, and select **Ubuntu** as the machine image
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742432931899/ec3110b4-e303-4315-a630-73476f393d1b.png align="center")
    
    As per Kubernetes documentation, a VM should have at least **2 GB RAM and 2 CPUs** for deployment. Select an instance type accordingly. Here, we choose **t2.medium** (2 vCPUs and 4 GB memory), which is suitable for Kubernetes deployment.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433008152/27dbcaeb-5e4a-477f-92d0-b1cda9e06034.png align="center")
    
    Generate a new **SSH Key Pair** for connecting to the instance.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433036827/67d86cfe-81d3-4ad0-8edc-1dc6185822cb.png align="center")
    
    Then, we need to configure network settings to open the required ports in the VM. As per Kubernetes documentation, the following ports need to be open in the control plane and worker nodes.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433095027/3156fbc6-0eee-4f01-bd4f-a8fe435ba96c.png align="center")
    
    Since we are deploying Kubernetes on a single EC2 instance (acting as both the control plane and worker node), all required ports must be open.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433145925/b0be2022-a526-411f-a92e-76c5faa6a620.png align="center")
    
    Configure storage for the VM by allocating **30GB** of **gp3** storage, as Kubernetes requires more space.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433196431/be650a4c-e8f5-4386-9deb-f2250cd7da70.png align="center")
    
    Finally, click on “Launch Instance“ to create a new instance with the configured settings.
    
    This will take some time to configure the VM and once the VM is ready, we can connect to VM using SSH and then start configuring Kubernetes inside this EC2 VM.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433260626/5cc68e01-26ae-454a-91fd-98ab02b1204d.png align="center")
    
4. **Connecting to EC2 Instance using SSH**
    
    We will use the previously generated SSH Key to connect to the EC2 instance.
    
    Here select ec2 instance and then click on connect
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433364571/578dd1d6-7892-40fc-91df-7e32c084ea69.png align="center")
    
    It will now provide us the commands that can be used to connect to VM using public DNS
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433391327/9bcca06e-6397-42c0-b520-bc8551bfc73e.png align="center")
    
    The following command is used
    
    ```bash
    ssh -i "ec2-kube-vm-key.pem" ubuntu@ec2-34-203-28-122.compute-1.amazonaws.com
    ```
    
    Once connected we get into Ubuntu CLI as shown below
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433438295/cdaa9cf3-6208-4c36-9067-1e5b2ec6e1b2.png align="center")
    
5. **Configuration of Kubernetes**
    
    1. Update and Install necessary packages and dependencies
        
        ```bash
        sudo apt update && sudo apt upgrade && sudo apt autoremove
        ```
        
    2. Update /etc/hosts with the hostname and private IP of the VM
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433514598/2377b13b-48d0-4e2a-a323-0c4d0e6b1023.png align="center")
        
    3. Reboot VM
        
        ```bash
        sudo reboot
        ```
        
    4. After some time connect to the VM again using SSH and then disable swap by running the following command.
        
        ```bash
        sudo swapoff -a
        ```
        
        Kubernetes requires the swap to be disabled for stable memory management.
        
    5. Load the required kernel modules by running the following commands
        
        ```bash
        sudo tee /etc/modules-load.d/containerd.conf <<EOF
        br_netfilter
        EOF
        
        sudo modprobe br_netfilter
        
        sudo sysctl -w net.ipv4.ip_forward=1
        
        lsmod | grep br_netfilter
        
        sysctl net.ipv4.ip_forward
        ```
        
        Here we load **br\_netfilter** which enables bridged traffic filtering allowing linux kernel to process network packets that pass through bridges and **net.ipv4.ip\_forward** enables IP forwarding allowing the system to act as a router or forward traffic between networks. Kubernetes uses container networking extensively, and these settings ensure proper communication between containers, pods, and nodes.
        
    6. **Install Container Runtime**
        
        A **container runtime** is a software responsible for running and managing containers on a host system. It handles tasks like pulling container images, creating and starting containers, allocating resources, and managing the lifecycle of containers. Kubernetes itself does not directly run containers—it relies on a **container runtime** to do so, hence we install a container runtime.
        
        Here I have installed containerd as container runtime as it is lightweight and efficient and backed by CNCF. Alternatively, you can also use other container runtimes like cri-o, docker, Mirantis Container Runtime.
        
        But before that install the necessary dependencies for containerd
        
        ```bash
        sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
        ```
        
        Download GPG keys for Docker’s official repository and add a new repository in the apt package manager’s list of sources. This allows us to install software from Docker’s official repository.
        
        ```bash
        sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
        
        sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
        ```
        
        Update the apt repository and then install containerd
        
        ```bash
        sudo apt update
        sudo apt install -y containerd.io
        ```
        
    7. **Configure Containerd for Kubernetes deployment**
        
        ```bash
        containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
        sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
        sudo systemctl restart containerd
        sudo systemctl enable containerd
        ```
        
        Enabling `SystemdCgroup = true` ensures compatibility with the host system's resource management (especially on systemd-based Linux distributions).
        
    8. **Install Kubernetes(Kubelet, Kubectl, Kubeadm)**
        
        For this too download the GPG public key for the Kubernetes package repository and add the Kubernetes repository to the system’s APT configuration which allows us to install Kubernetes components from the official source.
        
        ```bash
        curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
        
        echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
        ```
        
        Then update the repository and install Kubernetes
        
        ```bash
        sudo apt-get update
        
        sudo apt-get install -y kubelet kubeadm kubectl
        
        sudo apt-mark hold kubelet kubeadm kubectl
        ```
        
    9. **Initialize Kubernetes Cluster using Kubeadm**
        
        ```bash
        sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint=34.203.28.122
        ```
        
        Here we specify the IP address range(CIDR block) to be used for pod networking.
        
        This takes some time and on a successful run, you will get the following output. The `10.244.0.0/16` range means that pod IPs will be assigned from this subnet (e.g., `10.244.0.1`, `10.244.0.2`, etc.). This option is often required when using certain **CNI (Container Network Interface)** plugins like **Flannel**, which rely on a predefined pod network CIDR. Also, we specify the endpoint(Public IP address or DNS name of VM) that the control plane uses for communication
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433795071/c8d28937-a778-45cf-a9a3-50a6f2f8438e.png align="center")
        
        Here it describes future steps for configuring a multi-node kubernetes cluster which includes configuring kube config and joining worker nodes to the kubernetes control plane.
        
        As we are using a single node as both the control plane and worker node, we skip this step of joining and instead, we will taint the master node so that it acts as boththe master node and worker node in this single node Kubernetes cluster.
        
        ```bash
        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config
          
        kubectl taint nodes ip-172-31-26-199 node-role.kubernetes.io/cont
        rol-plane-
        ```
        
        In the current state, if we check the Kubernetes node status, it will be NotReady.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433848353/1f00508d-d4db-4e02-94e6-dcfee7f42647.png align="center")
        
        This is because we need to configure networking addon for kubernetes cluster. In our case, we will install flannel as networking addon.
        
    10. **Installing the flannel networking addon**
        
        We install the flannel addon by using the following manifest file
        
        flannel.yaml. **Flannel** is a popular **CNI (Container Network Interface)** plugin used in Kubernetes to provide networking between pods across different nodes in a cluster. It is a simple, lightweight, and easy-to-configure overlay network that enables communication between containers running on different nodes. In Kubernetes, each pod gets its own unique IP address, and these pods need to communicate with each other regardless of which node they are running on. Flannel ensures that pods can communicate seamlessly across the cluster by creating an overlay network.
        
        For larger clusters or environments requiring advanced networking features, we might consider alternatives like **Calico** , **Cilium** , or **Weave Net** .
        
        ```yaml
        ---
        kind: Namespace
        apiVersion: v1
        metadata:
          name: kube-flannel
          labels:
            pod-security.kubernetes.io/enforce: privileged
        ---
        kind: ClusterRole
        apiVersion: rbac.authorization.k8s.io/v1
        metadata:
          name: flannel
        rules:
        - apiGroups:
          - ""
          resources:
          - pods
          verbs:
          - get
        - apiGroups:
          - ""
          resources:
          - nodes
          verbs:
          - get
          - list
          - watch
        - apiGroups:
          - ""
          resources:
          - nodes/status
          verbs:
          - patch
        ---
        kind: ClusterRoleBinding
        apiVersion: rbac.authorization.k8s.io/v1
        metadata:
          name: flannel
        roleRef:
          apiGroup: rbac.authorization.k8s.io
          kind: ClusterRole
          name: flannel
        subjects:
        - kind: ServiceAccount
          name: flannel
          namespace: kube-flannel
        ---
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          name: flannel
          namespace: kube-flannel
        ---
        kind: ConfigMap
        apiVersion: v1
        metadata:
          name: kube-flannel-cfg
          namespace: kube-flannel
          labels:
            tier: node
            app: flannel
        data:
          cni-conf.json: |
            {
              "name": "cbr0",
              "cniVersion": "0.3.1",
              "plugins": [
                {
                  "type": "flannel",
                  "delegate": {
                    "hairpinMode": true,
                    "isDefaultGateway": true
                  }
                },
                {
                  "type": "portmap",
                  "capabilities": {
                    "portMappings": true
                  }
                }
              ]
            }
          net-conf.json: |
            {
              "Network": "10.244.0.0/16",
              "Backend": {
                "Type": "vxlan"
              }
            }
        ---
        apiVersion: apps/v1
        kind: DaemonSet
        metadata:
          name: kube-flannel-ds
          namespace: kube-flannel
          labels:
            tier: node
            app: flannel
        spec:
          selector:
            matchLabels:
              app: flannel
          template:
            metadata:
              labels:
                tier: node
                app: flannel
            spec:
              affinity:
                nodeAffinity:
                  requiredDuringSchedulingIgnoredDuringExecution:
                    nodeSelectorTerms:
                    - matchExpressions:
                      - key: kubernetes.io/os
                        operator: In
                        values:
                        - linux
              hostNetwork: true
              priorityClassName: system-node-critical
              tolerations:
              - operator: Exists
                effect: NoSchedule
              serviceAccountName: flannel
              initContainers:
              - name: install-cni-plugin
               #image: flannelcni/flannel-cni-plugin:v1.1.0 for ppc64le and mips64le (dockerhub limitations may apply)
                image: docker.io/rancher/mirrored-flannelcni-flannel-cni-plugin:v1.1.0
                command:
                - cp
                args:
                - -f
                - /flannel
                - /opt/cni/bin/flannel
                volumeMounts:
                - name: cni-plugin
                  mountPath: /opt/cni/bin
              - name: install-cni
               #image: flannelcni/flannel:v0.20.2 for ppc64le and mips64le (dockerhub limitations may apply)
                image: docker.io/rancher/mirrored-flannelcni-flannel:v0.20.2
                command:
                - cp
                args:
                - -f
                - /etc/kube-flannel/cni-conf.json
                - /etc/cni/net.d/10-flannel.conflist
                volumeMounts:
                - name: cni
                  mountPath: /etc/cni/net.d
                - name: flannel-cfg
                  mountPath: /etc/kube-flannel/
              containers:
              - name: kube-flannel
               #image: flannelcni/flannel:v0.20.2 for ppc64le and mips64le (dockerhub limitations may apply)
                image: docker.io/rancher/mirrored-flannelcni-flannel:v0.20.2
                command:
                - /opt/bin/flanneld
                args:
                - --ip-masq
                - --kube-subnet-mgr
                resources:
                  requests:
                    cpu: "100m"
                    memory: "50Mi"
                  limits:
                    cpu: "100m"
                    memory: "50Mi"
                securityContext:
                  privileged: false
                  capabilities:
                    add: ["NET_ADMIN", "NET_RAW"]
                env:
                - name: POD_NAME
                  valueFrom:
                    fieldRef:
                      fieldPath: metadata.name
                - name: POD_NAMESPACE
                  valueFrom:
                    fieldRef:
                      fieldPath: metadata.namespace
                - name: EVENT_QUEUE_DEPTH
                  value: "5000"
                volumeMounts:
                - name: run
                  mountPath: /run/flannel
                - name: flannel-cfg
                  mountPath: /etc/kube-flannel/
                - name: xtables-lock
                  mountPath: /run/xtables.lock
              volumes:
              - name: run
                hostPath:
                  path: /run/flannel
              - name: cni-plugin
                hostPath:
                  path: /opt/cni/bin
              - name: cni
                hostPath:
                  path: /etc/cni/net.d
              - name: flannel-cfg
                configMap:
                  name: kube-flannel-cfg
              - name: xtables-lock
                hostPath:
                  path: /run/xtables.lock
                  type: FileOrCreate
        ```
        
        Here in the flannel config please make sure that inside net-conf.json&gt; Network the POD CIDR mentioned matches the one used during kubeadm init command. Then apply the manifest by running the following command.
        
        ```yaml
         kubectl apply -f flannel.yaml
        ```
        
        Now again check the node status and pods. The node should be in ready status and pods should all be running by running the following commands.
        
        ```yaml
        kubectl get nodes
        kubectl get pods -n kube-system
        kubectl get pods -n kube-flannel
        ```
        
        The following output is expected.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742433964029/ff75647a-dcba-48ac-b951-64917d2f5fb2.png align="center")
        
    11. **Test Kubernetes cluster operation by deploying a simple hello world nginx server**
        
        To test whether the Kubernetes cluster is ready and capable of deploying applications, we will deploy a simple "Hello World" NGINX server application in the recently configured Kubernetes cluster.
        
        The following manifest file will be used for this "Hello World" application:
        
        hello-world.yaml
        
        ```yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: hello-world-deployment
        spec:
          replicas: 3
          selector:
            matchLabels:
              app: hello-world
          template:
            metadata:
              labels:
                app: hello-world
            spec:
              containers:
              - name: hello-world
                image: nginx:latest
                ports:
                - containerPort: 80
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: hello-world-service
        spec:
          selector:
            app: hello-world
          ports:
            - protocol: TCP
              port: 80
              targetPort: 80
          type: LoadBalancer
        ```
        
        Run this command to deploy this hello world application in a new namespace
        
        ```yaml
        kubectl create ns hello-world
        
        kubectl apply -f hello-world.yaml -n hello-world
        ```
        
        Now check the resources deployed in this new namespace by running the following command
        
        ```yaml
        kubectl get all -n hello-world
        ```
        
        The following output is expected.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742434074446/55174aae-08cc-4d8c-8601-247e505f58ae.png align="center")
        
        As the service is accessible over nodeport, we can access this hello-world application using the VM’s public IP and nodeport as shown below
        
        &lt;VM-PUBLIC-IP&gt;:&lt;NodePort&gt;
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742434091890/edba0101-7a42-4354-b877-8e19d2581387.png align="center")
        
        We can see that the application is running successfully. Hence this verifies the successful deployment of Kubernetes inside EC2 instance. Now we can use this for deploying other tools as per requirements for data engineering projects.
        

# Conclusion

To wrap up, deploying Kubernetes on AWS EC2 provides a flexible and cost-effective way to manage containerized workloads for data engineering projects. By automating the deployment, scaling, and management of applications, Kubernetes ensures efficient resource utilization and fault tolerance, which are critical for handling large-scale data pipelines, ETL jobs, and machine learning workloads. Through this blog, we demonstrated how to set up a Kubernetes cluster on an EC2 instance, configure networking with Flannel, and validate the setup by deploying a simple "Hello World" NGINX application. This foundation not only verifies the cluster's readiness but also showcases how Kubernetes can streamline infrastructure management, making it a powerful tool for modern data engineering workflows. With this setup, you can now confidently deploy and scale tools tailored to your project’s requirements, leveraging the scalability and flexibility of Kubernetes on AWS EC2.

# References

1. [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
    
2. [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
    
3. [https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd)
    
4. [https://github.com/flannel-io/flannel](https://github.com/flannel-io/flannel)
    
5. [Container Runtimes | Kubernetes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)