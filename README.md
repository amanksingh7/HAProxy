## HAProxy 

HAProxy, which stands for High Availability Proxy, is a popular free and open-source HTTP load balancer and reverse-proxy solution that helps web applications run highly available and provide the highest uptime.

As a load balancer, HAProxy works in two modes:
- A load balancer is an appliance for TCP connections, where judgments about balancing are made based on the entirety of the connection.
- A request for an HTTP balancer, in which each request is balanced independently.

### Pre-requisites
 - A system with Ubuntu 22.04 LTS OS.

### Steps to Install HAProxy


#### Update the package list:

     sudo apt update
   
#### View available haproxy package version from default repositories

    sudo apt show haproxy

#### Long term support release is HAProxy is 2.6, So install HAProxy 2.6. First enable PPA repository.

    sudo add-apt-repository ppa:vbernat/haproxy-2.6 -y
	
#### Now install HAProxy by command

	 sudo apt install -y haproxy=2.6.\*
	
#### After installltion, check HAProxy version.

    sudo haproxy -v

#### Once you see version of HAProxy, check status of HAproxy. and enable auto-start service.

     sudo systemctl status haproxy
	
#### Enable auto-start service, as whenever system reboot HAProxy will auto-start.

     sudo systemctl enable haproxy
			 
#### Now its time to take backup of configuration file for haproxy before start configuration.

      sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bk

