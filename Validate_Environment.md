## Verify Current State of Environment

Take Stock of the current docker containers in the environment.

```
[root@centos ~]# docker ps
```

There should not be any containers in exited or restarting state

```
# docker ps -a --filter status=exited --filter status=restarting
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS
```


After ensuring that the docker containers are in good state, we will
move on to checking the current state of the OpenStack environment.

The relevant files for this hands-on session can be under `/opt`

Verify basic OpenStack API calls.  The OpenStack API Calls should work
and we should not see any VM or Cinder volume in the environment.

```
[root@centos ~]# cd /opt
[root@centos opt]# ls
admin-openrc.sh  all-in-one  heat-test.yaml  init-runonce  kolla  kolla-ansible  multinode
[root@centos opt]# 
[root@centos opt]# source admin-openrc.sh 
[root@centos opt]# 
[root@centos opt]# nova list
+----+------+--------+------------+-------------+----------+
| ID | Name | Status | Task State | Power State | Networks |
+----+------+--------+------------+-------------+----------+
+----+------+--------+------------+-------------+----------+
[root@centos opt]# 
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
drifted by now.  Start NTP if service is down so that the VM can sync 
with the NTP Servers.  Check that the VM is syncing with the upstream
NTP Servers.

```
[root@centos opt]# systemctl enable ntpd.service
[root@centos opt]# systemctl start ntpd.service
[root@centos opt]# ntpq -pn
```
