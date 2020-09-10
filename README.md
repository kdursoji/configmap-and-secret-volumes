# configmap-and-secret-volumes
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ minikube start
😄  minikube v1.12.2 on Ubuntu 20.04
✨  Using the virtualbox driver based on existing profile

❗  'virtualbox' driver reported an issue: signal: killed
💡  Suggestion: Restart VirtualBox
📘  Documentation: https://minikube.sigs.k8s.io/docs/reference/drivers/virtualbox/

💣  Failed to validate 'virtualbox' driver
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ minikube delete
🔥  Deleting "minikube" in virtualbox ...
💀  Removed all traces of the "minikube" cluster.
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ minikube start
😄  minikube v1.12.2 on Ubuntu 20.04
✨  Automatically selected the virtualbox driver
👍  Starting control plane node minikube in cluster minikube
🔥  Creating virtualbox VM (CPUs=2, Memory=2200MB, Disk=20000MB) ...
🎉  minikube 1.13.0 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.13.0
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

🐳  Preparing Kubernetes v1.18.3 on Docker 19.03.12 ...
🔎  Verifying Kubernetes components...
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube"
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Stopped
kubeconfig: Configured

krishna@evoke:~/Containerization/configmap-and-secret-volumes$ ls
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl apply -f mosquitto-config-with-out-volumes.yaml 
Unable to connect to the server: dial tcp 192.168.99.107:8443: connect: no route to host
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ minikube delete
🔥  Deleting "minikube" in virtualbox ...
💀  Removed all traces of the "minikube" cluster.
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ minikube start
😄  minikube v1.12.2 on Ubuntu 20.04
✨  Automatically selected the virtualbox driver
👍  Starting control plane node minikube in cluster minikube
🔥  Creating virtualbox VM (CPUs=2, Memory=2200MB, Disk=20000MB) ...
🎉  minikube 1.13.0 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.13.0
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

🐳  Preparing Kubernetes v1.18.3 on Docker 19.03.12 ...
🔎  Verifying Kubernetes components...
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube"
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl apply -f mosquitto-config-with-out-volumes.yaml 
deployment.apps/mosquitto created
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
mosquitto-7fdbd86d7b-589tn   1/1     Running   0          18s
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl exec -it mosquitto-7fdbd86d7b-589tn -- /bin/sh
/ # ls
bin                   etc                   media                 proc                  sbin                  tmp
dev                   home                  mnt                   root                  srv                   usr
docker-entrypoint.sh  lib                   mosquitto             run                   sys                   var
/ # cd mosquitto/
/mosquitto # ls
config  data    log
/mosquitto # cd config/
/mosquitto/config # ls
mosquitto.conf
/mosquitto/config # vi mosquitto.conf 
/mosquitto/config # exit
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ ls
config-file.yaml  mosquitto-config-components-with-volumes.yaml  mosquitto-config-with-out-volumes.yaml  secret-file.yaml
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl apply -f config-file.yaml 
configmap/mosquitto-config-file created
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl apply -f secret-file.yaml 
secret/mosquitto-secret-file created
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl ger secrets
Error: unknown command "ger" for "kubectl"

Did you mean this?
	set
	get

Run 'kubectl --help' for usage.
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl ger secret
Error: unknown command "ger" for "kubectl"

Did you mean this?
	set
	get

Run 'kubectl --help' for usage.
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl get secret
NAME                    TYPE                                  DATA   AGE
default-token-khxmv     kubernetes.io/service-account-token   3      68m
mosquitto-secret-file   Opaque                                1      42s
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl get configmap
NAME                    DATA   AGE
mosquitto-config-file   1      72s
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ ls
config-file.yaml  mosquitto-config-components-with-volumes.yaml  mosquitto-config-with-out-volumes.yaml  secret-file.yaml
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ $kubectl apply -f mosquitto-config-components-with-volumes.yaml 

Command 'apply' not found, did you mean:

  command 'aptly' from deb aptly (1.3.0+ds1-4)

Try: sudo apt install <deb name>

krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl apply -f mosquitto-config-components-with-volumes.yaml 
configmap/mosquitto-config-file unchanged
secret/mosquitto-secret-file configured
deployment.apps/mosquitto configured
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
mosquitto-75f8589c97-4lnhg   1/1     Running   0          14s
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ kubectl exec -it mosquitto-75f8589c97-4lnhg -- /bin/sh
/ # ls
bin                   etc                   media                 proc                  sbin                  tmp
dev                   home                  mnt                   root                  srv                   usr
docker-entrypoint.sh  lib                   mosquitto             run                   sys                   var
/ # cd mosquitto/
/mosquitto # ls
config  data    log     secret
/mosquitto # cd secret/
/mosquitto/secret # ls
secret.file
/mosquitto/secret # cat secret.file 
somesupersecret file contents nobody should see/mosquitto/secret # cd ..
/mosquitto # ls
config  data    log     secret
/mosquitto # cd config/
/mosquitto/config # ls
mosquitto.conf
/mosquitto/config # cat mosquitto.conf 
log_dest stdout
log_type all
log_timestamp true
listener 9001
/mosquitto/config # exit
krishna@evoke:~/Containerization/configmap-and-secret-volumes$ 
