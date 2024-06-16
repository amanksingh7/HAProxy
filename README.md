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

#### Once you see version of HAProxy, check status of HAproxy and enable auto-start service.

     sudo systemctl status haproxy
	
#### Enable auto-start service, as whenever system reboot HAProxy will auto-start.

     sudo systemctl enable haproxy
			 
#### Now its time to take backup of configuration file for haproxy before start configuration.

      sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bk


#### Setting Configuration of HAProxy which file located in /etc/haproxy/haproxy.cfg.
	
      global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon

	defaults
        	log     global
        	mode    http
        	option  httplog
        	option  dontlognull
        	timeout connect 10s
        	timeout client  60s
        	timeout server  60s
        	errorfile 400 /etc/haproxy/errors/400.http
	        errorfile 403 /etc/haproxy/errors/403.http
	        errorfile 408 /etc/haproxy/errors/408.http
	        errorfile 500 /etc/haproxy/errors/500.http
	        errorfile 502 /etc/haproxy/errors/502.http
	        errorfile 503 /etc/haproxy/errors/503.http
	        errorfile 504 /etc/haproxy/errors/504.http

	frontend http_in
        	mode http
        	option httplog
        	bind *:80
        	option forwardfor
        	acl mnt_domina1 hdr(host) -i mnt.domina1.com
        	acl abc_domina2 hdr(host) -i abc.domain2.com


        	use_backend http_mnt if mnt_domina1
        	use_backend http_abc if abc_domina2


	backend http_mnt
        	mode http
        	option httplog
        	option forwardfor
        	server mnt 192.168.2.4:80

	backend http_abc
        	mode http
        	option httplog
	        option forwardfor
	        server abc 192.168.2.5:80

	frontend https_in
	        mode tcp
	        option tcplog
	        bind *:443
	        acl tls req.ssl_hello_type 1
	        tcp-request inspect-delay 5s
	        tcp-request content accept if tls
	
	        acl mnt_domina1 req.ssl_sni -i mnt.domina1.com
	        acl abc_domina2 req.ssl_sni -i abc.domina2.com
	
	        use_backend https_mnt if mnt_domina1
	        use_backend https_abc if abc_domina2

	backend https_mnt
	        mode tcp
	        option tcplog
	        option ssl-hello-chk
	        server mnt 192.168.2.4:443

	backend https_abc
	        mode tcp
	        option tcplog
	        option ssl-hello-chk
	        server mnt 192.168.2.5:443

## The above sections make up the HAProxy configuration file

- Global: This is the area that appears at the very top first. It has system-wide configuration options for security and performance optimization.
- Defaults: This section's settings, as the name implies, should function properly without further modification. Timeout and error reporting options are two 		examples of these settings.
- Frontend and Backend: The frontend and backend parameters are defined by these configurations. The HAProxy server will be designated as the front end for the 	frontend, distributing requests to the webservers that make up the backend servers. Additionally, we will configure HAProxy to distribute traffic 		according to round robin load balancing rules.
- Listen: You can enable HAProxy statistics monitoring by turning on this optional feature.
- We configured the web server nodes to listed to port 80 and 443 HAProxy configuration.

#### Once you save HAProxy configuration file, reload HAProxy service and check status.

     sudo systemctl reload haproxy && sudo systemctl status haproxy

