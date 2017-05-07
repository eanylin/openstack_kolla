## Environment Rebuild

We will tear down the existing in this exercise and rebuild it with
existing configurations to show that things will still work after that

The existing docker images in this environment are as follows:

```
[root@centos ~]# docker images
REPOSITORY                                                         TAG                 IMAGE ID            CREATED             SIZE
10.0.2.15:4000/kolla/centos-source-neutron-lbaas-agent             4.0.0               0d851398789f        6 hours ago         974.4 MB
10.0.2.15:4000/kolla/centos-source-bifrost-deploy                  4.0.0               b9b443c7eb21        6 hours ago         1.737 GB
10.0.2.15:4000/kolla/centos-source-neutron-vpnaas-agent            4.0.0               2ad7d337a1e1        6 hours ago         996.8 MB
10.0.2.15:4000/kolla/centos-source-neutron-server                  4.0.0               6526dadd36a1        6 hours ago         973.3 MB
10.0.2.15:4000/kolla/centos-source-neutron-sfc-agent               4.0.0               7ce97ab906fb        6 hours ago         948.8 MB
10.0.2.15:4000/kolla/centos-source-neutron-linuxbridge-agent       4.0.0               f0072f1199a7        6 hours ago         969.1 MB
10.0.2.15:4000/kolla/centos-source-glance-api                      4.0.0               ddf26e1a92f1        6 hours ago         917.6 MB
10.0.2.15:4000/kolla/centos-source-neutron-bgp-dragent             4.0.0               e038070db079        6 hours ago         946.7 MB
10.0.2.15:4000/kolla/centos-source-ironic-conductor                4.0.0               4d36d0dbe809        6 hours ago         1.032 GB
10.0.2.15:4000/kolla/centos-source-neutron-dhcp-agent              4.0.0               ad3159a35d55        6 hours ago         945.3 MB
10.0.2.15:4000/kolla/centos-source-neutron-l3-agent                4.0.0               ad3159a35d55        6 hours ago         945.3 MB
10.0.2.15:4000/kolla/centos-source-neutron-metadata-agent          4.0.0               ad3159a35d55        6 hours ago         945.3 MB
10.0.2.15:4000/kolla/centos-source-neutron-metering-agent          4.0.0               ad3159a35d55        6 hours ago         945.3 MB
10.0.2.15:4000/kolla/centos-source-neutron-openvswitch-agent       4.0.0               c837b9ff5f52        6 hours ago         945.3 MB
10.0.2.15:4000/kolla/centos-source-mistral-api                     4.0.0               c54a2ca4091f        6 hours ago         819.7 MB
...
10.0.2.15:4000/kolla/centos-source-base                            4.0.0               913ffa2be03a        7 hours ago         398.4 MB
docker.io/centos                                                   7                   a8493f5f50ff        3 weeks ago         192.5 MB
docker.io/registry                                                 2                   136c8b16df20        3 weeks ago         33.17 MB
[root@centos ~]# 
```

The most important file for building the kolla ansible environment
is the globals.yaml.  We are keeping most of the variables the same
other than environment specific ones (see below).

We need to make sure that we are pointing to the docker registry that
we set up on our node and that we are assigning an unused IP as the
internal VIP address.  We are using source build in this example, the
default is binary for CentOS.

The network_interface is the interface where all the API services will 
be bound to by default.  All the vxlan/tunnel and storage network traffic 
will pass through that as well.  It is enp0s3 in our case.

The neutron_external_interface is the interface given to neutron as its
external network port and should not have an IP assigned to it.  It is
enp0s8 in our case.

We have enabled Cinder and LVM as our cinder volume in this environment.
The services are disabled by default and should be turned on for our case.

```
vi /etc/kolla/globals.yml
---
kolla_install_type: "source"
openstack_release: "4.0.0"
kolla_internal_vip_address: "10.0.2.250"
docker_registry: "10.0.2.15:4000"
network_interface: "enp0s3"
neutron_external_interface: "enp0s8"
enable_cinder: "yes"
enable_cinder_backend_iscsi: "yes"
enable_cinder_backend_lvm: "yes"
cinder_volume_group: "cinder-volumes"
```
