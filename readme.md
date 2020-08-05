# Host Networking

## Set Up Switch - HyperV Networking

This section details how to set up a NAT network and VM Switch on a HyperV server.

Note that the examples below are using the `192.168.255.0/24` address space. Use of address ranges that do not conflict with locally available addresses is probably a good idea.

The below commands will:
- Create a new VM Switch
- Update associated vNIC on host
- Create a new NAT network and configure the IP range of that network
- Add a firewall rule to allow traffic from the VM to get to Packer on the host

```powershell
New-VMSwitch -SwitchName "NATSwitch" -SwitchType Internal
New-NetIPAddress -IPAddress 192.168.255.1 -PrefixLength 24 -InterfaceAlias "vEthernet (NATSwitch)"
New-NetNAT -Name "NATNetwork" -InternalIPInterfaceAddressPrefix 192.168.255.0/24
New-NetFirewallRule -DisplayName "Packer_http_server" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8000-9000
```

**Port forwarding rule.**


```powershell
netsh interface portproxy add v4tov4 listenport=8080 connectaddress=192.168.255.100 connectport=8999 protocol=tcp
```

# Run

This command 

```powershell
packer build -force -on-error=abort -var-file="data\centos8.json" .\centos-hyperv.json
```

# References

https://www.packer.io/docs/builders/hyperv/iso  
https://github.com/marcinbojko/hv-packer  
http://woshub.com/port-forwarding-in-windows