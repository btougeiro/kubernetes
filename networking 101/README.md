# networking 101

## example

### single node

- node with ip address 192.168.1.2 with a single pod (container)
- in kubernetes world an ip is assigned to a pod
- e.g. pod ip 10.244.0.2
- this ip is assigned because kubernetes creates an internal network  
  to assing the pod ips
- if you create more pods, each pod will receive an ip from this internal netrwork to communicate with each other
  through this ip
  
### multiple nodes

- ips 192.168.1.2 and 192.168.1.3
- same as before, one internal network to assign ips to the pods
- this will not work well because they have the same ips and you will get a conflict
- when a kubernetes cluster is set up, kubernetes won't assing automatically a network between nodes
- kubernetes expect from us to manage the network between the nodes

- facts:
    - all containers/pods can communicate to one another without NAT
    - all nodes can communicate with all containers and vice-versa without NAT

- we don't need to implement by ourselves these configurations
- we have some tools to handle this like:
    - cisco
    - flannel
    - vmware
    - nsx
    - cilium

- with these tools, we can communicate all nodes without conflict each other