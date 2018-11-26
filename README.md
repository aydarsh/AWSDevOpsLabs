# An example of a CI/CD stack on AWS featuring JIRA, Bitbucket and Bamboo
> You can download lab supporting materials with the following command:
```shell extension
$ git clone https://github.com/aydarsh/AWSDevOpsLabs.git
$ cd AWSDevOpsLabs/
```

This is an example of a CI stack build on Amazon Web Services.

[JIRA Software](https://www.atlassian.com/software/jira) is used for project and issue tracking,  
[Bitbucket](https://www.atlassian.com/software/bitbucket) is used for git code management,  
[Bamboo](https://www.atlassian.com/software/bamboo) is used for Continuous Integration.  

There are Cloud, Data Center and Server versions to use these products. I bought Server licenses 10$ each.

Here is the architecture diagram:  
![Continuous Integration](aws.png)

JIRA Software, Bitbucket and Bamboo servers are not accessible via Internet, since they are located in a private subnet. To reach the infrastructure developers connect via OpenVPN. Database instance is also located in a private subnet. For the servers in the private subnet to reach Internet a NAT instance is deployed in a public subnet.

OpenVPN instance is deployed in a public subnet. I used Bring Your Own License version of the OpenVPN server. It has a limit of two concurrently connected users. We are a team of a Developer and a DevOps engineer, so this version is just enough. An Elastic IP is attached to the OpenVPN server, so its public IP address remains the same across reboots. 


