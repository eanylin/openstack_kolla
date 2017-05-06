# OpenStack Kolla Ansible Hands-On Lab Session

This hands-on lab session will provide an overview of OpenStack Kolla and 
deployment using Ansible and Docker Containers.  Each participant will get 
a virtualbox image (Kolla All-In-One).  Docker images created via Kolla-Build 
will be included.  The OpenStack environment is CentOS based with OVS bridge 
and LVM Cinder.

The Virtualbox images for the lab session can be downloaded from [Google Drive](https://drive.google.com/drive/folders/0B4s2vD6eSZllZlFTdU1QZ001eXc?usp=sharing)


#### Verify Current State of Environment

Take Stock of the current docker containers in the environment.

```
[root@centos ~]# docker ps
```

There should not be any containers in exited or restarting state

```
# docker ps -a --filter status=exited --filter status=restarting
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS
```

Next, we will move on to checking the current state of the OpenStack 
environment.

The relevant files for this hands-on session can be under `/opt/opt`

Verify basic OpenStack API calls.  The OpenStack API Calls should be working
and should not have any VMs or Cinder volumes created.

```
[root@centos ~]# cd /opt
[root@centos opt]# ls
admin-openrc.sh  all-in-one  heat-test.yaml  init-runonce  kolla  kolla-ansible  multinode
[root@centos opt]# 
[root@centos opt]# source admin-openrc.sh 

[root@centos opt]# nova list
+----+------+--------+------------+-------------+----------+
| ID | Name | Status | Task State | Power State | Networks |
+----+------+--------+------------+-------------+----------+
+----+------+--------+------------+-------------+----------+

[root@centos opt]# cinder list
+----+--------+------+------+-------------+----------+-------------+
| ID | Status | Name | Size | Volume Type | Bootable | Attached to |
+----+--------+------+------+-------------+----------+-------------+
+----+--------+------+------+-------------+----------+-------------+
[root@centos opt]#

[root@centos opt]# openstack volume list

[root@centos opt]# openstack server list

[root@centos opt]# openstack stack list

[root@centos opt]#

```

As we are using snapshot image, the time on the VirtualBox VM will have
drifted by now.  Enable NTP if service is down so that the VM can sync 
with the NTP Servers.  Check that the VM is starting to sync with the
upstream NTP Servers.

```
[root@centos opt]# systemctl enable ntpd.service
[root@centos opt]# systemctl start ntpd.service
[root@centos opt]# ntpq -pn
```

#### Create Heat Stack

In this example we will create a single VM and attach a 1GB LVM Cinder volume 
to it.  We will pass the required values such as ssh key, flavor, network, image

```
openstack stack create -f yaml --parameter key_name=mykey --parameter image=cirros --parameter flavor=1 --parameter network=demo-net -t heat-test.yaml test_stack1
```

We will see print outs similar to the following:

```
[root@centos opt]# openstack stack create -f yaml --parameter key_name=mykey --parameter image=cirros --parameter flavor=1 --parameter network=demo-net -t heat-test.yaml test_stack1
id: 8be339af-0cac-4ca3-8483-3819601fc24b
stack_name: test_stack1
description: 'A HOT template that holds a VM instance with an attached Cinder volume.  The
  VM does nothing, it is only created.

  '
creation_time: '2017-04-30T00:29:23Z'
updated_time: null
stack_status: CREATE_IN_PROGRESS
stack_status_reason: Stack CREATE started
[root@centos opt]#
```

We can monitor the progress of the heat stack creation with the following command

```
watch openstack stack list
```

The heat should get created after waiting for a while

```
[root@centos opt]# openstack stack list
+--------------------------------------+-------------+----------------------------------+-----------------+----------------------+--------------+
| ID                                   | Stack Name  | Project                          | Stack Status    | Creation Time        | Updated Time |
+--------------------------------------+-------------+----------------------------------+-----------------+----------------------+--------------+
| 8be339af-0cac-4ca3-8483-3819601fc24b | test_stack1 | fea0597a2ea848e9a8812dabb6a4cb35 | CREATE_COMPLETE | 2017-04-30T00:29:23Z | None         |
+--------------------------------------+-------------+----------------------------------+-----------------+----------------------+--------------+
[root@centos opt]# 
[root@centos opt]# openstack server list
+--------------------------------------+--------------------------------------+--------+-------------------+------------+
| ID                                   | Name                                 | Status | Networks          | Image Name |
+--------------------------------------+--------------------------------------+--------+-------------------+------------+
| 7a2467d7-315f-4b08-aa70-25023bdab2e3 | test_stack1-my_instance-mhxeakev2dsq | ACTIVE | demo-net=10.0.0.5 | cirros     |
+--------------------------------------+--------------------------------------+--------+-------------------+------------+
[root@centos opt]# 
[root@centos opt]# openstack volume list
+--------------------------------------+---------------------------------+--------+------+---------------------------------------------------------------+
| ID                                   | Display Name                    | Status | Size | Attached to                                                   |
+--------------------------------------+---------------------------------+--------+------+---------------------------------------------------------------+
| e6228d85-3bc8-4e06-990c-85477a246453 | test_stack1-my_vol-pssfxzozubnp | in-use |    1 | Attached to test_stack1-my_instance-mhxeakev2dsq on /dev/vdb  |
+--------------------------------------+---------------------------------+--------+------+---------------------------------------------------------------+
[root@centos opt]#
```
