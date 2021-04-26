# website-on-ec2-bind9

## Solution of deploing web application on AWS using:
- free host DNS server - BIND9
- EC2 as a instance where website will be running, using NGINX
- registering free domain name on [freenom.com](https://www.freenom.com/en/index.html?lang=en)


### Lauch EC2 
1. Create EC2 [instruction](https://medium.com/@GalarnykMichael/aws-ec2-part-1-creating-ec2-instance-9d7f8368f78a)
2. Set Security Group with open to world port HTTP, and DNS (TCP and UDP) 

# Setup EC2 with Elastic IP
1. Create Elastic IP address
2. Associate it with EC2 instance [instructions](https://medium.com/@pablo_ezequiel/setting-an-elastic-ip-on-aws-ec2-739341a1cc65)

### Setup free domain Name
1. Find available domain on [Freenom.com](https://www.freenom.com/en/index.html?lang=en) and register
2. Set custom name servers in Manage Domain > Register Glue Records
3. Add ns1 as a hostname and paste static IP address of your EC2
4. Do the same with second nameserver: ns2
DNS server needs from 2 to 5 name servers records to increase reliability. NS records tell the Internet where to go to find out a domain's IP address.
In this case, one address IP can be used.

Example:
`ns1.machineworld.ml`
`ns2.machineworld.ml`

5. Save it
The domain registration is instant but the propagation of the domain’s DNS takes up to 72 hours.