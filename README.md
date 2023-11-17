# Network monitoring with prometheus and grafana
Network monitoring with prometheus and grafana

### Components
* Docker (v.4.22.0)
* prometheus (v.2.46.0)
* snmp exporter (v.0.23.0)
* grafana (v.10.0.3)


### Network configuration diagram


### System architecture


## Deploy
### Create snmp.yml file
* Get snmp.yml generater from https://github.com/prometheus/snmp_exporter.git.
* Generate snmp.yml with mibs.
* Copy the generated snmp.yml to the same directory.

### Router setting
* Configure the IP address, DHCP server, DNS and NAT (PAT).  
  ex.) cisco Router
    * IP address
      >int vlan 1  
      >ip add 192.168.1.254 255.255.255.0
      >int g 8  
      >ip add dhcp  
    * Default route (Last resort gateway)
      >ip route 0.0.0.0 0.0.0.0 dhcp
    * DHCP
      >service dhcp  
      >ip dhcp pool HOME  
      >network 192.168.1.0 255.255.255.0  
      >default-router 192.168.1.254
    * DNS
      >dns-server 8.8.8.8  
      >ip dns server
    * NAT
      >access-list 1 permit 192.168.1.0 0.0.0.255  
      >ip nat inside source list 1 int g 8 overload  
      >int vlan 1  
      >ip nat inside  
      >int g 8  
      >ip nat outside  
* Configure ACLs accordingly.

### L2SW setting
* Configure the IP address and default gateway.  
  ex.) cisco L2SW
    * IP address
      >int vlan 1  
      >ip add 192.168.1.99 255.255.255.0  
    * Default gateway
      >ip default-gateway 192.168.1.254  

* Enable snmp.  
  ex.) cisco L2SW  
    >snmp-server community snmp ro  
    >snmp-server enable traps  

    At this time, the community name is set to "snmp" as configured in snmp.yml.
 
### Set docker directory
* Fill the volume fields "prometheus", "grafana" and "snmp-exporter" in docer-compose.yml with the absolute path of this folder ("grafana" is not a volume field, but an env_file field).


### Configure prometheus
* Fill the targets field of the "snmp-exporter" job in prometheus.yml with the IP address of the L2SW.

* Fill the params-module field in the prometheus.yml with the module name that is written in snmp.yml.

### Execute docker-compose
ex.)
  >docker compose up