# An example of a Continuous Integration (CI) stack on AWS featuring JIRA, Bitbucket and Bamboo

This is an example of a CI stack build on Amazon Web Services. It's built for a team of one developer and one DevOps engineer.

[JIRA Software](https://www.atlassian.com/software/jira) is used for project and issue tracking,  
[Bitbucket](https://www.atlassian.com/software/bitbucket) is used for git code management,  
[Bamboo](https://www.atlassian.com/software/bamboo) is used for Continuous Integration.  

Here are some features of this stack:
* A user can create issues in JIRA
* A user can pull/push code from/to Bitbucket git repository
* Once code is merged with a project repository, Bamboo runs tests and builds the project
* A user can see project build status
* A user receives email notifications of issue status changes

There are Cloud, Data Center and Server versions to use these products. I bought Server licenses 10$ each.

Here is the architecture diagram:  
![Continuous Integration](aws.png)

JIRA Software, Bitbucket and Bamboo servers are not accessible via Internet, since they are located in a private subnet. To reach the infrastructure users connect via OpenVPN. Database instance is also located in a private subnet. For the servers in the private subnet to reach Internet a NAT instance is deployed in a public subnet.

Firstly, I create a custom VPC with one public subnet and one private subnet. I named it as "Development VPC". Development VPC is assigned default IPv4 CIDR block 10.0.0.0/16, public subnet 10.0.0.0/24 and private subnet 10.0.1.0/24.

Then, OpenVPN instance is deployed in a public subnet. I used Bring Your Own License version of the OpenVPN server. It has a limit of two concurrently connected users. An Elastic IP is attached to the OpenVPN server, so its public IP address remains the same across reboots. A separate security group "OpenVPN Access Server SG" is created and attached to this server. This security group allows inbound connections via 1194/udp, 22/tcp, 943/tcp and 443/tcp.

Next, an RDS PostgreSQL database instance is deployed in a private subnet. Here, at least two private subnets are required for a subnet group. So one more private subnet is created. This database instance has a separate security group "RDS PostgreSQL SG". It allows inbound connections via 5432/tcp from the default security group.

Then, one by one, three EC2 instances with JIRA, Bitbucket and Bamboo are deployed in a private subnet. These applications are linked with each other. Authentication on Bitbucket and Bamboo is configured to use JIRA. To configure email notification Mail Server credentials are needed. AWS has Simple Email Service (SES). New account in SES is created and the applications are configured to use these new credentials. JIRA, Bitbucket and Bamboo instances have default security group attached to them. Following port/protocols are allowed for inbound traffic in it: 8080/tcp, 22tcp, 7990/tcp, 7993/tcp, 8085/tcp, 7999/tcp, 8005/tcp, 7992/tcp. "OpenVPN Access Server SG" is shown as the source of traffic for the default security group.

As soon as a user connects to the infrastructure via OpenVPN, he/she can use a Web browser to reach JIRA, Bitbucket and Bamboo via their private IP addresses. Additionally, Amazon Route 53 DNS service is used to create an internal DNS zone resource.local. CNAMEs jira.resource.local, bitbucket.resource.local, bamboo.resource.local that point to internal DNS names are created. A user can use these addresses to reach the applications.

Finally, we get a Continuous Integration stack, where a user can create issues in JIRA, can push code to Bitbucket git repository, and Bamboo tests/builds a project as soon as there is a code merge to a repository.
