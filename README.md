# website-on-ec2-bind9

## Solution of deploing web application on AWS using:
- free host DNS server - BIND9
- EC2 as a instance where website will be running, using NGINX
- registering free domain name on [freenom.com](https://www.freenom.com/en/index.html?lang=en)


### Lauch EC2 
1. Create EC2 [instruction](https://medium.com/@GalarnykMichael/aws-ec2-part-1-creating-ec2-instance-9d7f8368f78a)
2. Set Security Group with open to world port HTTP, and DNS (TCP and UDP) 
