
# AWS

## EKS architecture:

![](https://miro.medium.com/v2/resize:fit:2000/1*B7f0pdQcoT9HKCJ5mUtuzg.png)

### Worker nodes:
![](https://miro.medium.com/v2/resize:fit:2000/0*rG-gau-zP9_j7LWX.png)
- These nodes are a group of EC2 instances provisioned by AWS. 
- Each Amazon EC2 node is deployed to one subnet. 
- Each node is assigned a private IP address from a CIDR block assigned to the subnet.

## IP Range:

Well, in an IPv4 address space, there's a total of 32 bits.
![None](https://miro.medium.com/v2/resize:fit:700/1*K-qGk8aq454KsWt5FgmI3g.png)

The number that comes after the slash tells us how many bits are  **part of**  the network address (and ultimately what is left over, we can assign).

So let's take the IP address  **192.168.10.0/24.**

**The "/24" here indicates that the first 24 bits are part of the network address (192.168.10) leaving only the remaining 8 bits able to be changed for specific host addresses (0-254).**

(meaning first 8+8+8 are constant and the rest will be in range so `192.168.10` is constant and rest can be in range from 0 to 254 )
**The /16 will have first 2 constant 8+8 `192.168` 
rest can be variable meaning  255 × 255**

### **A Practical Example**

Let's say we choose  **10.0.0.0/24**  for a virtual network. This means for that virtual network you will have available the ranges 10.0.0.1–10.0.0.254 

Then you create a second virtual network using  **10.0.0.0/8**  which ranges from `10.0.0.1–10.255.255.254`.

The problem here is that some addresses may overlap which could be a problem. For example, `they both have the capability to use 10.0.0.8 which will not work.`

However, let's redo this situation and say you create one virtual network using 10.0.0.0/16 (ranging 10.0.0.1–10.0.255.254) and another using 10.1.0.0/16 (10.1.0.1–10.1.255.254), you will have no overlap.

## Security Group vs NACL:

![What is a Security Group](https://static.javatpoint.com/tutorial/aws/images/aws-nacl-vs-security-group.png)

**NACLs operate at the subnet level and control traffic in and out of a VPC, while Security Groups operate at the instance level and control traffic to and from individual EC2 instances**.

A NACL is a security layer for your VPC, that acts as a firewall for controlling traffic in and out of one or more subnets.

![](https://cdn.shortpixel.ai/spai/w_1152+q_lossy+ret_img+to_webp/www.corestack.io/wp-content/uploads/img7-1024x456-1.jpg)

![](https://cdn.shortpixel.ai/spai/w_1152+q_lossy+ret_img+to_webp/www.corestack.io/wp-content/uploads/img10-1024x303-1.jpg)

![](https://cdn.shortpixel.ai/spai/w_1152+q_lossy+ret_img+to_webp/www.corestack.io/wp-content/uploads/img12-1024x475-1.jpg)

![](https://cdn.shortpixel.ai/spai/w_1136+q_lossy+ret_img+to_webp/www.corestack.io/wp-content/uploads/img13-1024x799-1.jpg)

![](https://learnwithaniket.com/wp-content/uploads/2021/08/Network_ACL_Outbound_Rules-1024x529.jpg)

-   Every rule has a number associated with it.
-   Every NACL has a rule with number as asterisk (*). This rule can not be modified. If there is no match then this rule is applied.
- NACLs are stateless, ingress does not equal egress. Traffic that matches a rule for one direction will **_not_** be automatically allowed in the opposite direction. You would have to add an outbound rule.
_You have now blocked all traffic to and from those associated subnets on port 22._

_**NAT Gateway:**_  _A Network Address Translation (NAT) allows instances in your private subnet to connect to outside services like Databases but restricts external services to connecting to these instances._

- Internet gateway allows the internet to reach the instance and vice versa. NAT allows only one way communication (instance to internet). A resource over the internet cannot initiate a connection to a resource using NAT, but can using an IGW.

- NAT ensures that your instance in the private subnet is truly private. Even if a SG allows all traffic on a given port, no resource outside the VPC could connect to it. Extra layer of security and compliance.
- **If the instance needs internet but will not accept external connections, use NAT.**
- A Network Access Control List (NACL) is _stateless_. This means the rules are enforced in both directions. Thus, in your scenario, traffic would be blocked in _both directions_.

![How to create a security group using the AWS Management Console?](https://www.manageengine.com/log-management/images/amazon-vpc-security-groups-ss3-24.png)

You can either add security group rules now or after creating the security group.
![How to create a security group using the AWS Management Console?](https://www.manageengine.com/log-management/images/amazon-vpc-security-groups-ss4-24.png)

Security Groups are stateful, ingress equals egress. Traffic that matches a rule for one direction will also be allowed automatically in the opposite direction.

![](https://cdn.shortpixel.ai/spai/w_1136+q_lossy+ret_img+to_webp/www.corestack.io/wp-content/uploads/img4.jpg)

![](https://cdn.shortpixel.ai/spai/w_1488+q_lossy+ret_img+to_webp/www.corestack.io/wp-content/uploads/img5.jpg)

## Subnet:
A subnet, or subnetwork, is **a network inside a network**.
- A subnet is essentially a range of IP (v4 and v6) addresses.
- A subnet must live within a single Availability Zone.
- -   **Private Subnet:**  If there is no route to the internet in the Route table associated with the subnet then the subnet is said to be a Private subnet.  
    **Use Case:**  Database servers and other application servers that should not be exposed to the internet are present in Private Subnet.  
    
- -   **Public Subnet:**  If there is an internet gateway attached to the Route table of the subnet.  
    **Use Case:**  Web Servers and other servers that need direct internet access are placed in Public subnets.
![Default VPC](https://cdn.hashnode.com/res/hashnode/image/upload/v1699528395651/kSuv86jll.png?auto=format&auto=compress,format&format=webp)

Contrary to RDS, there is no such option for EC2 instances. **They are created in a subnet and if you want multi-az you will need to launch multiple instances in different subnets across the availability zones.**
you could then create an ELB and configure this ELB to distribute the traffic between them. Bear in mind that the ELB will reserve one private IP in each of the subnets.

Availability Zone A and B have divided VPC of IP range `10.0.0.0/16` to `10.0.0.0/24` and `10.0.0.0/24` 
## VPC:
![](https://miro.medium.com/v2/resize:fit:1400/1*j65csQd0VgvDS6dYyWVFPg.png)

-   A VPC spans all availability zones in a region.
- A VPC can have multiple subnets


## ECS vs EKS:
**Elastic Container Service**
-   ECS relies on AWS-provided services like ALB,  [Route 53](https://www.bmc.com/blogs/an-introduction-to-aws-route-53/), etc.,
-   EKS handles all these mechanisms internally, just as in any old Kubernetes cluster.
- EKS kubernates Control plane service where etcd, etc stays is managed by AWS.
-    AWS ECS has limited ability in the way of readiness/liveness probes. Only a container health check is available in the task definition.
-  The number of containers you can run on a single ECS instances vs. managed EKS is significant. ( 120 tasks vs. 750 pods).

-   AWS EKS can scale intra-region. AWS ECS can only scale in the same region.
- EKS is a kubernetes solution, ECS is a "docker-compose" solution kind if.

--- 
## Load Balancers:

AWS provides 2 load balancers:
	1. Application Load Balancer
	2. Network Load Balancer

### Network load Balancer:
1.  NLB runs at the transport layer which is  **OSI layer 4**
2. Support protocols like TCP and UDP
![AWS network load balancer workflow](https://devopscube.com/wp-content/uploads/2023/08/nw-load-balancer.gif)



### Application Load Balancer:
1. ALB supports routing rules like path-based routing.
2. Supports protocols like HTTP, HTTPS, HTTP/2, gRPC, WebSockets, etc.
3. ALB runs at the application layer, which is  **OSI layer 7**.
4. You can add ASG to Target Group that is mapped to a ALB.
![AWS application load balancer  workflow](https://devopscube.com/wp-content/uploads/2023/08/appliation-load-balancer.gif)

> ### Key Differences 

1. The network load balancer just forward requests whereas the application load balancer examines the contents of the HTTP request header to determine where to route the request

2. Network load balancing cannot assure availability of the application, where as Application load balancing can.

4. NLB cant direct traffic to Lamdba functions.

3. So, if you want requests for the v1 API endpoint to be routed to an autoscaling group of EC2 instances and requests for the v2 API endpoint routed to another group of instances, then your best option is the ALB because it allows you to configure rules that make your desired routing possible.

	On the other hand, if you just want that clients coming from Germany are routed to one autoscaling group and clients from the USA to another group, the NLB should be sufficient because you can set up rules that match the IP addresses of those countries.

> ### **What is a target group?**

A  **target group**  contains EC2 instances to which a  **load balancer**  distributes workload.
