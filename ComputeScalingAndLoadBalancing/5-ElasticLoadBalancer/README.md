## Elastic Load Balancer Architecture (ELB)
![](./ELBArch.png)

- You can use Ipv4 or dualstack
- Required to pick ONE subnet in EACH of the TWO or more AZ
- ELB will then deploy load balancer nodes into each of those subnet
- ELB will have a single A record which points to all of the ELB nodes (Distributed equally)
- ELB is HA as well, will scale accordingly with load in the Subnet you have provided
- It can be Internet facing (will have both private and public ip addresses) or Internal facing (only have private ip address)
- Listener configuration is the configuration of how to handle the incoming network (Eg. Which port or protocol) 
- ELB needs 8 or more free IP addresses in the subnets For EG. /28 -> 16 ip addresses - 5 AWS reserved = 11 free per subnet which only leave 3 free ip address if ELB is deployed
- AWS recommends at least /27 subnet