## installation

### cases
* high performance => ssd backed storage
* multiple concurrent connections => network based storage
* shared access across multiple pods => persistent shared volumes
* label nodes with specific disk types
  * use node selectors to assign applications to nodes with specific disk types

### nodes
* virtual or physical machines
* minimum of 4 nodes cluster (m3.medium on aws)
* master nodes preferably shouldn't host workloads (taint)

### installation options
* minikube
  * deploys VMs
  * single node cluster
* kubeadm
  * requires VMs to be ready
  * single/multi node cluster
  
### hosted vs. tunable solutions
* hosted
  * managed kubernetes cluster
    * EKS
    * GKE
    * openshift online
* tunable
  * scripted deployment/management of kubernetes cluster, makes it easy to deploy kubernetes cluster privately within an organization
    * openshift
    * cloud foundry container runtime
    * vmware cloud pks
    * vargant

### high availability
* kube-api server
  * can be ran in active-active mode
  * in case of multiple master nodes it is required to have a load balancer in front of master nodes, so that requests are split between the master nodes
* controller manager and scheduler
  * should be ran in active-standby mode
    * must not run in parallel to avoid request duplication
    * achieved through leader-election process
      ```bash
      # kube-controller tries to become a leader every 2 seconds, in case other master node crashes
      # current leader renews the lease (leadership) evrey 15s
      kube-controller-manager --leader-elect true --leader-elect-retry-period 2s --leader-elect-lease-duration 15s
      ```
      * scheduler follows the same approach
* etcd
  * can be part of master node (stacked control plane topology) (eaasier to manage/setup)
  * can be ran on a seprate servers (external etcd topology) (more reliable)
    * kube-api server accepts list of etcd servers
      ```
      kube-apiserver --etcd-servers=https://10.240.0.10:2379,https://10.40.0.11:2379
      ```
    * kube-api server can read/write to any of the etcd servers