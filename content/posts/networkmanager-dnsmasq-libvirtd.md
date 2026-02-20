+++
title = "NetworkManager dnsmasq - libvirtd"
date = "2021-01-08"
description = "Using NetworkManager's dnsmasq plugin with libvirtd."
+++

Once configured, you'll be able to talk to libvirtd VM's using their hostname, rather than having to know the IP address.

# Forward DNS requests to libvirt's Domain

Libvirt uses it's own built-in dnsmasq instance for serving DHCP and DNS to the VM's in the virtual network.

We'll configure NetworkManager's dnsmasq to forward requests to the libvirt instance.

**/etc/NetworkManager/dnsmasq.d/libvirt.conf**

```ini
server=/vm/192.168.122.1
```

Now when we try to access **hostname**.vm, we'll be forwarded to `192.168.122.1` (the IP for libvirt's default virtual network DHCP server).

When testing with terraform, usign the libvirt provider, I create a terraform specific virtual network. So I have `server=/terraform.vm/10.0.100.1` included in this config file (**hostname**.terraform.vm).

# Configuring libvirt

Now we need to configure libvirt so that it resolves DNS requests with this domain name.

Either using the XML editor in virt-manager or `virsh --connect qemu:///system net-edit default`:

```xml
<network>
  <name>default</name>
  <uuid>82539b71-a83e-4fb9-9cb4-af22f89206b0</uuid>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:51:bd:3f'/>
  <!-- Add this line -->
  <domain name='vm' localOnly='yes'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
```
