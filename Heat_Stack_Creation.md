## Working with Heat Stack

### Example 1

In this example we will create a single VM and attach a 1GB LVM Cinder volume 
to it.  We will pass the required values such as ssh key, flavor, network and image

```
openstack stack create -f yaml --parameter key_name=mykey \
                               --parameter image=cirros \
                               --parameter flavor=1 \
                               --parameter network=demo-net \
                               -t heat-test.yaml test_stack1
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

Delete the Heat Stack

```
[root@centos opt]# openstack stack delete test_stack1
Are you sure you want to delete this stack(s) [y/N]? y
[root@centos opt]# openstack stack list
+--------------------------------------+-------------+----------------------------------+--------------------+----------------------+----------------------+
| ID                                   | Stack Name  | Project                          | Stack Status       | Creation Time        | Updated Time         |
+--------------------------------------+-------------+----------------------------------+--------------------+----------------------+----------------------+
| 8be339af-0cac-4ca3-8483-3819601fc24b | test_stack1 | fea0597a2ea848e9a8812dabb6a4cb35 | DELETE_IN_PROGRESS | 2017-04-30T00:29:23Z | 2017-04-30T00:37:29Z |
+--------------------------------------+-------------+----------------------------------+--------------------+----------------------+----------------------+
[root@centos opt]# openstack stack list

[root@centos opt]#
```

### Example 2

We will test floating IP assignment and remote SSH into the Cirros VM in this example.
Note that we are using host-only adpater network and hence will not be able to reach
the internet from our Cirros VM.

Download the HOT template from the following [link](https://raw.githubusercontent.com/eanylin/openstack_kolla/master/single_vm_floating_ip_heat.yaml)

```
[root@centos opt]# wget https://raw.githubusercontent.com/eanylin/openstack_kolla/master/single_vm_floating_ip_heat.yaml
```

Create the Heat Stack with the HOT template

```
openstack stack create --parameter image=cirros \
                       --parameter flavor=1 \
                       --parameter key_name=mykey \
                       --parameter private_net_id=demo-net \
                       --parameter private_subnet_id=$(neutron subnet-list | grep demo-subnet | awk '{print $2}') \
                       --parameter public_net_id=public1 \
                       -t single_vm_floating_ip.yaml test_stack01
```

Check the progress of the stack creation and wait for it to complete to get the floating IP of the VM:

```
[root@centos opt]# openstack stack list
+--------------------------------------+--------------+----------------------------------+-----------------+----------------------+--------------+
| ID                                   | Stack Name   | Project                          | Stack Status    | Creation Time        | Updated Time |
+--------------------------------------+--------------+----------------------------------+-----------------+----------------------+--------------+
| e7d729d9-284e-47d9-90d6-1e5a179f1954 | test_stack01 | fea0597a2ea848e9a8812dabb6a4cb35 | CREATE_COMPLETE | 2017-04-30T00:08:15Z | None         |
+--------------------------------------+--------------+----------------------------------+-----------------+----------------------+--------------+
[root@centos opt]#
[root@centos opt]# openstack server list
+--------------------------------------+---------+--------+-----------------------------------+------------+
| ID                                   | Name    | Status | Networks                          | Image Name |
+--------------------------------------+---------+--------+-----------------------------------+------------+
| e5aeb06e-fa54-4a66-9ed7-51d743a324db | Server1 | ACTIVE | demo-net=10.0.0.5, 192.168.56.151 | cirros     |
+--------------------------------------+---------+--------+-----------------------------------+------------+
[root@centos opt]# 
```

Check that you can ping and SSH to the Cirros VM:

```
MacBook-Pro:openstack_kolla root$ ping -c 3 192.168.56.151
PING 192.168.56.151 (192.168.56.151): 56 data bytes
64 bytes from 192.168.56.151: icmp_seq=0 ttl=63 time=0.929 ms
64 bytes from 192.168.56.151: icmp_seq=1 ttl=63 time=0.986 ms
64 bytes from 192.168.56.151: icmp_seq=2 ttl=63 time=1.476 ms

--- 192.168.56.151 ping statistics ---
3 packets transmitted, 3 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 0.929/1.130/1.476/0.246 ms
MacBook-Pro:openstack_kolla root$ 
MacBook-Pro:openstack_kolla root$ telnet 192.168.56.151 22
Trying 192.168.56.151...
Connected to 192.168.56.151.
Escape character is '^]'.
SSH-2.0-dropbear_2012.55
^]
telnet> quit
Connection closed.
MacBook-Pro:openstack_kolla root$ 
MacBook-Pro:openstack_kolla root$ ssh 192.168.56.151 -l cirros
The authenticity of host '192.168.56.151 (192.168.56.151)' can't be established.
RSA key fingerprint is SHA256:P901iyzz3YTWLitmWSyi4xIAFdE7CJPN5kNlyEK58LA.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.56.151' (RSA) to the list of known hosts.
cirros@192.168.56.151's password: 
$ ifconfig
eth0      Link encap:Ethernet  HWaddr FA:16:3E:F5:2D:B7  
          inet addr:10.0.0.5  Bcast:10.0.0.255  Mask:255.255.255.0
          inet6 addr: fe80::f816:3eff:fef5:2db7/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:145 errors:0 dropped:0 overruns:0 frame:0
          TX packets:164 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:25170 (24.5 KiB)  TX bytes:16541 (16.1 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

$ 
```

Delete the Heat Stack

```
[root@centos opt]# openstack stack delete test_stack01
Are you sure you want to delete this stack(s) [y/N]? y
[root@centos opt]# 
```

Hence we can see that Kolla Ansible is able to bring up an OpenStack 
environment with Docker containers and performs the same tasks as 
traditional KVM/VM approach for bringing up the environment.
