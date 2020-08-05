# kube-shell

How many times have you been asked to troubleshoot applications running in a Kubernetes cluster? Well, I have faced this problem multiple times. Troubleshooting/debugging excersise includes running pings, tcpdumps, DNS lookups, running `kubectl` to access Kubernetes objects likes pods, servies and so on. 

Most of the solutions have been executing a Linux container using `kubectl`, and then accessing the container using  `kubectl exec` and/or `kubectl proxy` to get access to a Linux shell to run commands. 

How about we run this debugging process the same way we run applications within a Kubernetes cluster? How about using Kubernetes objects like `LoadBalancers` and `Ingress` to access the Linux shell? This project attempts to do such a thing. It uses a well known terminal project - `ttyd` - to enable Shell access into a Kubernetes cluster using a browser. 

You can run this container in your Kubernetes cluster as per the instructions provided below or follow the directions to build your own container images with your site specific customizations and then run them as an application within your Kubernetes cluster. The example provided here uses the service type `LoadBalancer` to expose the application but can be modified to be exposed as an `Ingress` object easily. The solution presented here provided both SSL and non-SSL based web access to the Shell application. 

## Docker image setup (optional)

YOu can use the two sample Dockerfiles provided here to create your own images. The Dockerfiles use a two stage build to setup the enviornment. 
* Lines 1-3 are used to setup the enviornment and compile the ttyd binary. 
* Lines 5-14 are used to create the final base image, using the Ubuntu base image, required binares and software packages necessary to perform troubleshooting/debugging activities as well as the ttyd binary from the first step.
* Lines 16-22 sets up the configuration for the container image. 

The container image can be executed directly on a system [IPADDRESS_1 / hostname] running Docker - 

with SSL
```
docker run -p 443:7681 -v [path to ssl certs]:/etc/ttyd_certs:ro  [container_image:tag]
```
access the application on a client browser by navigating to https://[IPADDRESS_1 /hostname]


