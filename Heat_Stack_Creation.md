## Create Heat Stack

In this example we will create a single VM and attach a 1GB LVM Cinder volume 
to it.  We will pass the required values such as ssh key, flavor, network and image

```
openstack stack create -f yaml --parameter key_name=mykey --parameter image=cirros --parameter flavor=1 --parameter network=demo-net -t heat-test.yaml test_stack1
```

We should see print outs similar to the following:

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

We can monitor the progress of the heat stack creation with the following command:

```
watch openstack stack list
```

The heat stack should get created after waiting for a while

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
