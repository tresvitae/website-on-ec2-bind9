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

## Install and configure BIND9 DNS server on Ubuntu
1. `apt-get update`
2. `apt install bind9 bind9-doc dnsutils`

BIND stands for Berkley Internet Naming Daemon. Domain Name Service is an Internet service that maps IP addresses and fully qualified domain names (FQDN) to one another. In this way, DNS alleviates the need to remember IP addresses. Computers that run DNS are called name servers.

dnsutils is useful package for testing and troubleshooting DNS issues.

The DNS configuration files are stored in the /etc/bind directory. The primary configuration file is /etc/bind/named.conf, which in the layout provided by the package just includes these files.

/etc/bind/named.conf.options: global DNS options
/etc/bind/named.conf.local: for your zones
/etc/bind/named.conf.default-zones: default zones such as localhost, its reverse, and the root hints

3. If not, enable the new configuration service
`systemctl enable bind9`

4. Store all the logs to /var/log/named/bind.log
`echo 'include "/etc/bind/named.conf.log";' | tee -a /etc/bind/named.conf`
```sh
tee /etc/bind/named.conf.log << EOF
logging {
 channel bind_log {
   file "/var/log/named/bind.log" versions 3 size 5m;
   severity info;
   print-category yes;
   print-severity yes;
   print-time yes;
 };
 category default { bind_log; };
 category update { bind_log; };
 category update-security { bind_log; };
 category security { bind_log; };
 category queries { bind_log; };
 category lame-servers { null; };
};
EOF
```

5. Create a directory for storing logs and set necessary privileges
`mkdir /var/log/named`
`chown bind:root /var/log/named`
`chmod 775 /var/log/named/`

6. Restart the service
`systemctl restart bind9`

7. Setup log rotation
```sh
tee /etc/logrotate.d/bind << EOF
/var/log/named/bind.log
{
    rotate 90
    daily
    dateext
    dateformat _%Y-%m-%d
    missingok
    create 644 bind bind
    delaycompress
    compress
    notifempty
    postrotate
        /bin/systemctl reload bind9
    endscript
}
EOF
```

8. Replace zone name (machinedie.ml) to your domain name from Freenom.com 
```sh
\tee /etc/bind/named.conf.local << EOF
zone "machinedie.ml" {
        type master;
        file "/etc/bind/zones/db.machinedie.ml";
};
EOF
```
