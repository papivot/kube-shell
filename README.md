# kube-shell

How many times have you been asked to troubleshoot applications running in a Kubernetes cluster? Well, I have faced this problem multiple times. Troubleshooting/debugging excersise includes running pings, tcpdumps, DNS lookups, running `kubectl` to access Kubernetes objects likes pods, servies and so on. 

Most of the solutions have been executing a Linux container using `kubectl`, and then accessing the container using  `kubectl exec` and/or `kubectl proxy` to get access to a Linux shell to run commands. 

How about we run this debugging process the same way we run applications within a Kubernetes cluster? How about using Kubernetes objects like `LoadBalancers` and `Ingress` to access the Linux shell? *kube-shell* attempts to do such a thing. It uses a well known terminal project - `ttyd` - to enable Shell access into a Kubernetes cluster using a browser. 

You can run this application in your Kubernetes cluster as per the instructions provided below or follow the directions to build your own container images with your site specific customizations and then run them as an application within your Kubernetes cluster. The example provided here uses the service type `LoadBalancer` to expose the application but can be modified to be exposed as an `Ingress` object easily. The solution presented here provided both SSL and non-SSL based web access to the Shell application. 

### Sample use cases 
(NOT FOR PRODUCTION USE CASE BUT FOR TROUBLESHOOTING AND DEMO)
* A restricted non-expiring `kubectl` access to the Kubernetes cluster. Modify the RBAC of the application service account to control what access you grant to `kubectl`
* A non-terminating Bash shell within a namespace to troubleshoot networking, services as well as learn about the internal workings of container and Kubernetes technology.
* Web access to the Bash shell makes it easy for DevOps users.

## Docker image setup (optional)

You can use the two sample Dockerfiles provided here to create your own images. The Dockerfiles use a two stage build to setup the enviornment. 
* Lines 1-3 are used to setup the enviornment and compile the ttyd binary. 
* Lines 5-14 are used to create the final base image, using the Ubuntu base image, required binares and software packages necessary to perform troubleshooting/debugging activities as well as the ttyd binary from the first step. Modify these lines to add /modify binaries and utilities of your choice. 
* Lines 16-22 sets up the configuration for the container image. Modify these lines to configure the ttyd settings and enable/disable SSL.

The container image can be executed directly on a system [IPADDRESS_1 or HOSTNAME ] running Docker - 

#### With SSL
```
docker run -p 443:7681 -v [path to ssl certs]:/etc/ttyd_certs:ro  [container_image:tag]
```
where [path to ssl certs] contains the SSL cert and its private key file. This is mounted to /etc/ttyd_certs folder during the execution.
Now access the kube-shell application on a client browser by navigating to https://[IPADDRESS_1 or HOSTNAME ]

#### Without SSL
```
docker run -p 80:7681 [container_image:tag]
```
Now access the kube-shell application on a client browser by navigating to http://[IPADDRESS_1 or HOSTNAME ]

## Running in a Kubernetes cluster

Use the 2 sample YAML files provided to run the app within a Kubernetes cluster (with SSL or without SSL support). As mentioned previously, the current setup uses service type `LoadBalancer` to expose the application but can be tweaked to be exposed by an `Ingress` object.
If you are creating your own container images, you will need to modify the yaml files to point to the new image locations. 

#### Without SSL

```
kubectl apply -f kube-shell-http.yaml
```
Wait for the pod to change to ta running state and the service has an external IP address associated to. 

```
kubectl get svc -n kube-shell
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
kube-shell   LoadBalancer   10.99.12.163   35.239.42.201   80:30390/TCP   17m
```
Modify your DNS to point to the new external IP address reported by the service `kube-shell`

#### With SSL 

Create a Kubernetes secret with the tls.crt and tls.key file. YOu may need to create a `kube-shell`, if it has not already been created.  
```
kubectl create ns kube-shell
kubectl -n kube-shell create secret tls ssl-pem --cert=tls.crt --key=tls.key
```

```
kubectl apply -f kube-shell-https.yaml
```
Wait for the pod to change to a running state and the service has an external IP address associated to. 

```
kubectl get svc -n kube-shell
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
kube-shell   LoadBalancer   10.99.12.163   35.239.42.201   443:30390/TCP   17m
```
Modify your DNS to point to the new external IP address reported by the service `kube-shell`

Enjoy !!!
