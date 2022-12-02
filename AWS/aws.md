
# All About AWS:

1. Each Customer would have a account or no of accounts which is the base of AWS architechture: **ACCOUNT**

2. To Deal with Latency and other issues deal with compliance policies. AWS has launched its cloud in multiple regions across the world. which can be called as **REGION**

3. In a **region** you launch your actual application. You will have your own logical data center given to you by default, known as             VPC — Virtual Private Cloud. 

    it like a new on-premise branch of yours.

4. Let’s say most of your customers are from N.Virginia and you have launched a really great application stack in that region, in your VPC. Unfortunately, the place where the real data center in N.Virginia is, got hit by a terrible earthquake and DC is no more operational. To deal with these kind of problems AWS came up with multiple zones in a region simply known as an **Availability Zone**. 

    An **availability zone** may have multiple Physical Data Centers with it’s own power supply, cooling and physical security. All the AZs in a region are connected via redundant and ultra low-latency networks. 

    AWS considers factors like power distribution, floodplains, and tectonics when placing **Availability Zones** within a region so that each **availability zone** has a different risk profile.


5. Inside the **availability zone** is where we will create actual sub-networks — considering **VPC** is our entire big network, we can create multiple sub-networks based on the requirements to make the network as efficient and secure as possible. which is called **Subnet**.

     <center><img title="a title" alt="Alt text" src="https://miro.medium.com/max/1400/1*ZHt34Boz4quQ5D9nq4MKXQ.webp"></center>


6. EC2: is a VM. We can install Kubernates on top of EC2 and Run as similar to EKS, but in EKS we have a managed offering.
    Amazon Elastic Compute Cloud (EC2) is the Amazon Web Service you use to create and run virtual machines in the cloud (we call these virtual machines 'instances').
    
     <center><img title="a title" alt="Alt text" src="https://res.cloudinary.com/practicaldev/image/fetch/s--7Ntoevf0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/6ss4tm7zf6pbvis2ik4c.png"></center>

     <center><img title="a title" alt="Alt text" src="https://res.cloudinary.com/practicaldev/image/fetch/s--AiuI0FxJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/8yw2h5bg5oevf9es93fr.png"></center>

    <center><img title="a title" alt="Alt text" src="https://res.cloudinary.com/practicaldev/image/fetch/s--mwyeHH_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/ll4paggcd0m45dnk8ves.png"></center>


    ECR — ECR stands for Elastic Container Registry. It is basically used as a Docker Container Registry. We can create repositories in ECR and maintain docker images inside a repository and pull and push them accordingly.

    > NOTE: EC2s with Instance Storage cannot be stopped or rebooted as they are ephemeral in nature, they can only be terminated. But EBS backed EC2s can be rebooted, stopped and started without Data Loss.

    S3 — S3 stands for Simple Storage Service. S3 is an extremely simple cloud storage service by AWS. It stores everything as an object in buckets. S3 objects can range in size from a minimum of 0 bytes to a maximum of 5 terabytes.
    


> With IaaS services, such as Amazon EC2, your company can consume compute servers, known as “instances”, on-demand. This means that the hardware and software stack, up to the operating system is managed for you.

> Examples of database PaaS offerings include Amazon RDS.

> Examples of SaaS services: Google Apps, Microsoft Office 365, and Salesforce.
7. For Database there is RDS.

8. DynamoDB — Amazon DynamoDB is a NoSQL database which basically supports key-value and document data structures. 

9. Network:

    The primary network components in your account are the following—

        Internet Gateway

        Implicit Router(Virtual)

        Route Tables

        NACL — Network Access Control Lists

        NAT Gateway

        Security Groups

     <center><img title="a title" alt="Alt text" src="https://miro.medium.com/max/1400/1*dJ6mjVisKyMR5wtlRKtP3A.webp"></center>

    To create anything in the account, VPC should exist first, so we’ll start with VPC.

    CIDR:

    When creating a VPC, we need to specify a CIDR block which will be our primary network of the VPC. This primary network can be further split into subnets. 
    The biggest IPv4 CIDR block that can be created is /16 and the smallest that can be created is /28.

    > For example —Biggest:x.x.x.x/16 [10.0.0.0/16] & Smallest: x.x.x.x/28[10.0.0.0/28]. We cannot go below /16 and beyond /28.

    This CIDR block is always considered private by AWS irrespective of the range we use, as the IPs used from this range are only used for Private communication between the instances and not for Public/Internet communication. So even if we specify 150.0.0.0/16 which is a public IP Range, AWS treats this as a private IP and you cannot reach the internet using these IPs. `For public communication we can ask AWS to assign us an Public or Elastic IP to the instance.`

    ## Elastic IP, Public IP and Private IP:

    **Elastic IP** — Elastic IP is a combination of static and public IP in AWS that can be added to your account and can be assigned to instances whenever required.

    Use Case: Lets say you hosted a service which should be accessible from the Internet. So you assign an elastic IP, so that this service is reachable via this EIP. In case the instance where the service is hosted went down, you can still remap it to a different instance and make the service available on the same IP.

    **Public IP** — Unlike Elastic IP, this is dynamic Public IP. You will get a public IP to an instance which is reachable via internet. But in case you stop your instance and start it later, you would get a completely different Public IP address.

    **Private IP** — This IP is mandatory and is used for internal communication between instances within a VPC or across multiple VPCs in case of VPC Peering is set up. More on VPC Peering in a different article. This IP is given from the CIDR range we assigned when we created the VPC.


    Internet Gateway

    As the name suggests it’s a device which will allow you to reach internet. Think of your home Router/Access Point which simply does the same job — allows your devices to communicate with internet. But the only difference is this IGW is horizontally scalable, highly available unlike your home access point where you will need to restart sometimes.

    > You can only attach one IGW to a VPC at any time. By default the maximum no of IGWs allowed per region are 5.

    Implicit Router: holds the Route Table in **VPC**. This is implicit and can't be accessed by **USER**. Route Table is public and can be accessed from **VPC**.

    Route Table: 

    These tables are no different from your Route Tables in a Home Based Router/Access Points. These will tell how your resources in one subnet can reach the Internet or any other resources in any other subnet. A route table can be associated with multiple subnets, however one subnet can only be associated with one route table at a time. A route table in AWS looks like this —

    <center><img title="a title" alt="Alt text" src="https://miro.medium.com/max/1400/1*J5Eb2JRGX3IPfmlEIM5olA.webp"></center>

    It has destination and a Target. Destination can be a single IP address or an entire CIDR block. Target is the actual resource in AWS where the traffic gets routed Target can be one of these -`[IGW, Instance, Egress only IGW, NAT Gateway, Network Interface, Outpost Local Gateway, Peering Connection, Transit Gateway, Virtual Private Gateway]`.


    NACL — Network Access Control List

    Basically a NACL in AWS is a stateless firewall — meaning you have to explicitly tell what should be allowed and what should be denied. So there should be Set of Inbound and Outbound Rules both specifying Allowed and Denied Traffic. `By default NACL allows ALL inbound and outbound traffic.` Like Route Tables, NACLs can also be associated to multiple subnets, however one subnet can only be associated with one NACL at a time. A default NACL in AWS looks like this —

     <center><img title="a title" alt="Alt text" src="https://miro.medium.com/max/1400/1*wYK37el8DcUWWRDiltsBvQ.webp"></center>

    NAT Gateway

    NAT stands for Network Address Translation and basically enables your Resources in a Private Subnet communicate with internet without assigning each resource a Public IPv4 Address. I specifically mentioned IPv4 address because, 
    
    > an IPv6 address has no concept of Private/Public Address. Each IPv6 address is a public IP and a Global Unicast Address which means anyone on the internet can reach out to this address, given there are no NACLs written to deny traffic to this address.

    So the best use case of a NAT Gateway would be — when you want your resources in Private Subnet access internet, but prevent the internet from initiating a connection to those Resources.

    Security Group

    Unlike NACL, Security Group is a Stateful Firewall — meaning if inbound traffic is allowed by you, outbound response from your resource is automatically allowed irrespective of your Outbound Rules. Security Group can be stateful by keeping track of connections. The primary difference between a Security Group and NACL is this — Security Group is Stateful while a NACL is not, it doesn’t keep track of any connections. Security Group works at instance level while a NACL works at Subnet level. So you can think of Security Group as a Firewall to an Instance — it can be an EC2 instance or a Database instance.
