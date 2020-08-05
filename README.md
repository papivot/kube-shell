# kube-shell

How many times have you been asked to troubleshoot applications running in a Kubernetes cluster? Well, I have faced this problem multiple times. Troubleshooting/debugging excersise includes running pings, tcpdumps, DNS lookups, running `kubectl` to access Kubernetes objects likes pods, servies and so on. 

Most of the solutions have been executing a Linux container using `kubectl`, and then accessing the container using  `kubectl exec` and/or `kubectl proxy` to get access to a Linux shell to run commands. 

How about we run this debugging process the same way we run applications within a Kubernetes cluster? How about using Kubernetes objects like `LoadBalancers` and `Ingress` to access the Linux shell? This project attempts to do such a thing. It uses a well known terminal project - `ttyd` - to enable Shell access into a Kubernetes cluster using a browser. 
