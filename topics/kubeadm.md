
## kubeadm

kubeadm is a tool built to provide kubeadm init and kubeadm join as best-practice “fast paths” for creating Kubernetes clusters

* good tool for on-prem deployment
* kubeadm init executes all the steps needed to bring up a cluster
  ```
  preflight                    Run pre-flight checks
  kubelet-start                Write kubelet settings and (re)start the kubelet
  certs                        Certificate generation
    /ca                          Generate the self-signed Kubernetes CA to provision identities for other Kubernetes components
    /apiserver                   Generate the certificate for serving the Kubernetes API
    /apiserver-kubelet-client    Generate the certificate for the API server to connect to kubelet
    /front-proxy-ca              Generate the self-signed CA to provision identities for front proxy
    /front-proxy-client          Generate the certificate for the front proxy client
    /etcd-ca                     Generate the self-signed CA to provision identities for etcd
    /etcd-server                 Generate the certificate for serving etcd
    /etcd-peer                   Generate the certificate for etcd nodes to communicate with each other
    /etcd-healthcheck-client     Generate the certificate for liveness probes to healthcheck etcd
    /apiserver-etcd-client       Generate the certificate the apiserver uses to access etcd
    /sa                          Generate a private key for signing service account tokens along with its public key
  kubeconfig                   Generate all kubeconfig files necessary to establish the control plane and the admin kubeconfig file
    /admin                       Generate a kubeconfig file for the admin to use and for kubeadm itself
    /kubelet                     Generate a kubeconfig file for the kubelet to use *only* for cluster bootstrapping purposes
    /controller-manager          Generate a kubeconfig file for the controller manager to use
    /scheduler                   Generate a kubeconfig file for the scheduler to use
  control-plane                Generate all static Pod manifest files necessary to establish the control plane
    /apiserver                   Generates the kube-apiserver static Pod manifest
    /controller-manager          Generates the kube-controller-manager static Pod manifest
    /scheduler                   Generates the kube-scheduler static Pod manifest
  etcd                         Generate static Pod manifest file for local etcd
    /local                       Generate the static Pod manifest file for a local, single-node local etcd instance
  upload-config                Upload the kubeadm and kubelet configuration to a ConfigMap
    /kubeadm                     Upload the kubeadm ClusterConfiguration to a ConfigMap
    /kubelet                     Upload the kubelet component config to a ConfigMap
  upload-certs                 Upload certificates to kubeadm-certs
  mark-control-plane           Mark a node as a control-plane
  bootstrap-token              Generates bootstrap tokens used to join a node to a cluster
  kubelet-finalize             Updates settings relevant to the kubelet after TLS bootstrap
    /experimental-cert-rotation  Enable kubelet client certificate rotation
  addon                        Install required addons for passing Conformance tests
    /coredns                     Install the CoreDNS addon to a Kubernetes cluster
    /kube-proxy                  Install the kube-proxy addon to a Kubernetes cluster
  ```
* kubeadm init phase allows to specify phases to be executed
   ```bash
   # run all sub-steps from control-plane step
   kubeadm init phase control-plane controller-manager 

   # run controller-manager sub-step from control-plane step
   kubeadm init phase control-plane controller-manager 
   ```
 * to start using cluster after running kubeadm init create kubeconfig
   ```
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```
 * reset a cluster
   ```bash
   # kill all the resources
   kubeadm reset
   
   # clean ip table
   iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X
   ```
