# Nova Compute
- Install package
```
yum install openstack-nova-compute -y
```
- Configure
```
crudini --set /etc/nova/nova.conf DEFAULT enabled_apis osapi_compute,metadata
crudini --set /etc/nova/nova.conf DEFAULT transport_url rabbit://openstack:RABBIT_PASS@10.100.100.10
crudini --set /etc/nova/nova.conf DEFAULT my_ip 10.100.100.20
crudini --set /etc/nova/nova.conf DEFAULT use_neutron True
crudini --set /etc/nova/nova.conf DEFAULT firewall_driver nova.virt.firewall.NoopFirewallDriver

crudini --set /etc/nova/nova.conf vnc enabled True
crudini --set /etc/nova/nova.conf vnc server_listen 0.0.0.0
crudini --set /etc/nova/nova.conf vnc server_proxyclient_address 10.100.100.20
crudini --set /etc/nova/nova.conf vnc novncproxy_base_url http://10.100.100.10:6080/vnc_auto.html

crudini --set /etc/nova/nova.conf glance api_servers http://10.100.100.10:9292
crudini --set /etc/nova/nova.conf oslo_concurrency lock_path /var/lib/nova/tmp

crudini --set /etc/nova/nova.conf placement os_region_name RegionOne
crudini --set /etc/nova/nova.conf placement project_domain_name Default
crudini --set /etc/nova/nova.conf placement project_name service
crudini --set /etc/nova/nova.conf placement auth_type password
crudini --set /etc/nova/nova.conf placement user_domain_name Default
crudini --set /etc/nova/nova.conf placement auth_url http://10.100.100.10:5000/v3
crudini --set /etc/nova/nova.conf placement username placement
crudini --set /etc/nova/nova.conf placement password PLACEMENT_PASS

crudini --set /etc/nova/nova.conf api auth_strategy keystone

crudini --set /etc/nova/nova.conf keystone_authtoken auth_url http://10.100.100.10:5000/v3
crudini --set /etc/nova/nova.conf keystone_authtoken memcached_servers 10.100.100.10:11211
crudini --set /etc/nova/nova.conf keystone_authtoken auth_type password
crudini --set /etc/nova/nova.conf keystone_authtoken project_domain_name Default
crudini --set /etc/nova/nova.conf keystone_authtoken user_domain_name Default
crudini --set /etc/nova/nova.conf keystone_authtoken project_name service
crudini --set /etc/nova/nova.conf keystone_authtoken username nova
crudini --set /etc/nova/nova.conf keystone_authtoken password NOVA_PASS

crudini --set /etc/nova/nova.conf libvirt virt_type qemu
```
- Start service
```
systemctl enable libvirtd.service openstack-nova-compute.service
systemctl start libvirtd.service openstack-nova-compute.service
systemctl status libvirtd.service openstack-nova-compute.service
```
- Add compute node to cell database
Run this on controller node
```
source admin_rc
openstack compute service list --service nova-compute
su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova
```
- Verify
```
openstack compute service list
```