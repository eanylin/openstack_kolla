## Environment Rebuild

We will tear down the existing environment in this exercise and rebuild it
from scratch

### Pre-Checks

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
is the globals.yaml.  We are keeping most of the variables with its
default value and making changes to those that are environment specific.

We need to make sure that we are pointing to the docker registry that
we set up on our node and that we are assigning an unused IP as the
internal VIP address.  We are using source build in this example (default
for CentOS is binary).

The instructions for setting up the docker registry can be found at the
official Kolla document [link](https://docs.openstack.org/project-deploy-guide/kolla-ansible/ocata/multinode.html)

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

The next thing that we need to check will be the passwords.yaml.  It is
locate at `/etc/kolla/passwords.yml`.  All the passwords should be populated
in this file.  Deployment engineers can change the passwords prior to the
deployment.  The file has already been generated in our case.  The command to
generate random passwords is as follows:

```
kolla-genpwd
```

Further information can also be found at the official Kolla Ansible documentation
[link](https://docs.openstack.org/developer/kolla-ansible/quickstart.html)


### Tear Down Environment

We are now ready to tear down our environment

Execute the following commands from `/opt` folder to clean up the containers
and host configuration files (**Do not delete any of the docker images as it 
will take time to download them again**)

```
kolla-ansible/tools/cleanup-containers

kolla-ansible/tools/cleanup-hosts
```

The output should look similar to this

```
[root@centos opt]# kolla-ansible/tools/cleanup-containers 
Removing ovs bridge...
Guru meditation now registers SIGUSR1 and SIGUSR2 by default for backward compatibility. SIGUSR1 will no longer be registered in a future release, so please use SIGUSR2 to generate reports.
Stopping containers...
Removing containers...
Removing volumes...
All cleaned up!
[root@centos opt]# 
[root@centos opt]# 
[root@centos opt]# kolla-ansible/tools/cleanup-host
Creating a fstab backup...
Unmounting Ceph OSD disks
Removing ceph references from fstab...
Getting folders name...
removed ‘/etc/kolla/cinder-api/config.json’
removed ‘/etc/kolla/cinder-api/cinder.conf’
removed directory: ‘/etc/kolla/cinder-api’
removed ‘/etc/kolla/cinder-backup/config.json’
...
removed directory: ‘/etc/kolla/rabbitmq’
removed ‘/etc/kolla/tgtd/config.json’
removed directory: ‘/etc/kolla/tgtd’
[root@centos opt]#
```

Check that only the registry docker container is left in the environment

```
[root@centos opt]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
cb9f7e51bc7b        registry:2          "/entrypoint.sh /etc/"   7 days ago          Up 3 minutes        0.0.0.0:4000->5000/tcp   registry
[root@centos opt]#
```


### Kolla Ansible Deploy

Start Deployment

```
kolla-ansible deploy -i all-in-one
```

Deployment time is dependent on the underlying host and will vary between
individuals.

The end result will look similar to the following:

```
...
PLAY RECAP *************************************************************************************************************************************************************************************************
localhost                  : ok=251  changed=153  unreachable=0    failed=0   
```

### Basic Sanity Tests

Execute the following commands under `/opt` folder:

```
source admin-openrc.sh 

./init-runonce 
```

Make use of the output of `init-runonce` to create a demo VM

The end result will look similar to the following:

```
[root@centos opt]# openstack server create \
>     --image cirros \
>     --flavor m1.tiny \
>     --key-name mykey \
>     --nic net-id=0c7de303-7d23-44a7-a2d7-e28bfffe5b87 \
>     demo1
+-------------------------------------+-----------------------------------------------+
| Field                               | Value                                         |
+-------------------------------------+-----------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                        |
| OS-EXT-AZ:availability_zone         |                                               |
| OS-EXT-SRV-ATTR:host                | None                                          |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                          |
| OS-EXT-SRV-ATTR:instance_name       |                                               |
| OS-EXT-STS:power_state              | NOSTATE                                       |
| OS-EXT-STS:task_state               | scheduling                                    |
| OS-EXT-STS:vm_state                 | building                                      |
| OS-SRV-USG:launched_at              | None                                          |
| OS-SRV-USG:terminated_at            | None                                          |
| accessIPv4                          |                                               |
| accessIPv6                          |                                               |
| addresses                           |                                               |
| adminPass                           | H93CyC8otetQ                                  |
| config_drive                        |                                               |
| created                             | 2017-05-07T03:51:46Z                          |
| flavor                              | m1.tiny (1)                                   |
| hostId                              |                                               |
| id                                  | b9823dd2-4718-4f44-96e2-fa9aa4664808          |
| image                               | cirros (6b8aec48-87da-4c4a-b6bd-68628c1e8630) |
| key_name                            | mykey                                         |
| name                                | demo1                                         |
| progress                            | 0                                             |
| project_id                          | 1ddf226f081f4e879e3c2555cac880bd              |
| properties                          |                                               |
| security_groups                     | name='default'                                |
| status                              | BUILD                                         |
| updated                             | 2017-05-07T03:51:46Z                          |
| user_id                             | 11a97488e81942388b586e744d4c5f81              |
| volumes_attached                    |                                               |
+-------------------------------------+-----------------------------------------------+
[root@centos opt]# nova list
+--------------------------------------+-------+--------+------------+-------------+-------------------+
| ID                                   | Name  | Status | Task State | Power State | Networks          |
+--------------------------------------+-------+--------+------------+-------------+-------------------+
| b9823dd2-4718-4f44-96e2-fa9aa4664808 | demo1 | ACTIVE | -          | Running     | demo-net=10.0.0.7 |
+--------------------------------------+-------+--------+------------+-------------+-------------------+
[root@centos opt]# 
```

At this point in time we have successfully deployed the all-in-one (AIO) environment
from scratch using Kolla Ansible :smile:
