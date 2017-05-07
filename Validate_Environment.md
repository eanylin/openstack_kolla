## Verify Current State of Environment

Take Stock of the current docker containers in the environment.

```
[root@centos ~]# docker ps
CONTAINER ID        IMAGE                                                                COMMAND                  CREATED             STATUS              PORTS                    NAMES
038e85c9c5d4        10.0.2.15:4000/kolla/centos-source-horizon:4.0.0                     "kolla_start"            4 hours ago         Up 4 hours                                   horizon
67923a763b37        10.0.2.15:4000/kolla/centos-source-heat-engine:4.0.0                 "kolla_start"            4 hours ago         Up 4 hours                                   heat_engine
26efd0d26b46        10.0.2.15:4000/kolla/centos-source-heat-api-cfn:4.0.0                "kolla_start"            4 hours ago         Up 4 hours                                   heat_api_cfn
b8d4a0e8ec5d        10.0.2.15:4000/kolla/centos-source-heat-api:4.0.0                    "kolla_start"            4 hours ago         Up 4 hours                                   heat_api
40d4b84f11de        10.0.2.15:4000/kolla/centos-source-neutron-metadata-agent:4.0.0      "kolla_start"            4 hours ago         Up 4 hours                                   neutron_metadata_agent
452f135ced49        10.0.2.15:4000/kolla/centos-source-neutron-l3-agent:4.0.0            "kolla_start"            4 hours ago         Up 4 hours                                   neutron_l3_agent
f628c2c47306        10.0.2.15:4000/kolla/centos-source-neutron-dhcp-agent:4.0.0          "kolla_start"            4 hours ago         Up 4 hours                                   neutron_dhcp_agent
f0753f062fdd        10.0.2.15:4000/kolla/centos-source-neutron-openvswitch-agent:4.0.0   "kolla_start"            4 hours ago         Up 4 hours                                   neutron_openvswitch_agent
71220697b5e4        10.0.2.15:4000/kolla/centos-source-neutron-server:4.0.0              "kolla_start"            4 hours ago         Up 4 hours                                   neutron_server
f5d1bc213ee6        10.0.2.15:4000/kolla/centos-source-openvswitch-vswitchd:4.0.0        "kolla_start"            4 hours ago         Up 4 hours                                   openvswitch_vswitchd
b38d7018b2d7        10.0.2.15:4000/kolla/centos-source-openvswitch-db-server:4.0.0       "kolla_start"            4 hours ago         Up 4 hours                                   openvswitch_db
59dde27705bd        10.0.2.15:4000/kolla/centos-source-nova-compute:4.0.0                "kolla_start"            4 hours ago         Up 4 hours                                   nova_compute
603e9714709d        10.0.2.15:4000/kolla/centos-source-nova-novncproxy:4.0.0             "kolla_start"            4 hours ago         Up 4 hours                                   nova_novncproxy
caa999b5e017        10.0.2.15:4000/kolla/centos-source-nova-consoleauth:4.0.0            "kolla_start"            4 hours ago         Up 4 hours                                   nova_consoleauth
ae013c3bebb7        10.0.2.15:4000/kolla/centos-source-nova-conductor:4.0.0              "kolla_start"            4 hours ago         Up 4 hours                                   nova_conductor
beee18823561        10.0.2.15:4000/kolla/centos-source-nova-scheduler:4.0.0              "kolla_start"            4 hours ago         Up 4 hours                                   nova_scheduler
32d850ee1e0f        10.0.2.15:4000/kolla/centos-source-nova-api:4.0.0                    "kolla_start"            4 hours ago         Up 4 hours                                   nova_api
7870d2bda5e5        10.0.2.15:4000/kolla/centos-source-nova-placement-api:4.0.0          "kolla_start"            4 hours ago         Up 4 hours                                   placement_api
7d11433c9d27        10.0.2.15:4000/kolla/centos-source-nova-libvirt:4.0.0                "kolla_start"            4 hours ago         Up 4 hours                                   nova_libvirt
41086532deaf        10.0.2.15:4000/kolla/centos-source-nova-ssh:4.0.0                    "kolla_start"            4 hours ago         Up 4 hours                                   nova_ssh
dabd530259f1        10.0.2.15:4000/kolla/centos-source-cinder-backup:4.0.0               "kolla_start"            4 hours ago         Up 4 hours                                   cinder_backup
a7beb6ee519c        10.0.2.15:4000/kolla/centos-source-cinder-volume:4.0.0               "kolla_start"            4 hours ago         Up 4 hours                                   cinder_volume
6f2f15325ca8        10.0.2.15:4000/kolla/centos-source-cinder-scheduler:4.0.0            "kolla_start"            4 hours ago         Up 4 hours                                   cinder_scheduler
201effa24bca        10.0.2.15:4000/kolla/centos-source-cinder-api:4.0.0                  "kolla_start"            4 hours ago         Up 4 hours                                   cinder_api
99fa4fd7374e        10.0.2.15:4000/kolla/centos-source-glance-registry:4.0.0             "kolla_start"            4 hours ago         Up 4 hours                                   glance_registry
e95aa4a240a0        10.0.2.15:4000/kolla/centos-source-glance-api:4.0.0                  "kolla_start"            4 hours ago         Up 4 hours                                   glance_api
2a4cfec30c3f        10.0.2.15:4000/kolla/centos-source-keystone:4.0.0                    "kolla_start"            4 hours ago         Up 4 hours                                   keystone
4daf8506bb4e        10.0.2.15:4000/kolla/centos-source-rabbitmq:4.0.0                    "kolla_start"            4 hours ago         Up 4 hours                                   rabbitmq
6fd8c8ad3b0c        10.0.2.15:4000/kolla/centos-source-tgtd:4.0.0                        "kolla_start"            4 hours ago         Up 4 hours                                   tgtd
28ed01a4eb3b        10.0.2.15:4000/kolla/centos-source-iscsid:4.0.0                      "kolla_start"            4 hours ago         Up 4 hours                                   iscsid
e47aa3a89c41        10.0.2.15:4000/kolla/centos-source-mariadb:4.0.0                     "kolla_start"            4 hours ago         Up 4 hours                                   mariadb
921dda41d864        10.0.2.15:4000/kolla/centos-source-memcached:4.0.0                   "kolla_start"            4 hours ago         Up 4 hours                                   memcached
870f2a7f602d        10.0.2.15:4000/kolla/centos-source-keepalived:4.0.0                  "kolla_start"            4 hours ago         Up 4 hours                                   keepalived
578634936fa0        10.0.2.15:4000/kolla/centos-source-haproxy:4.0.0                     "kolla_start"            4 hours ago         Up 4 hours                                   haproxy
db5af7e39f70        10.0.2.15:4000/kolla/centos-source-cron:4.0.0                        "kolla_start"            4 hours ago         Up 4 hours                                   cron
9abcd4f6428d        10.0.2.15:4000/kolla/centos-source-kolla-toolbox:4.0.0               "kolla_start"            4 hours ago         Up 4 hours                                   kolla_toolbox
eb4a7bc6d4b9        10.0.2.15:4000/kolla/centos-source-fluentd:4.0.0                     "kolla_start"            4 hours ago         Up 4 hours                                   fluentd
cb9f7e51bc7b        registry:2                                                           "/entrypoint.sh /etc/"   8 hours ago         Up 4 hours          0.0.0.0:4000->5000/tcp   registry
[root@centos ~]# 
```

There should not be any containers in exited or restarting state

```
# docker ps -a --filter status=exited --filter status=restarting
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS
```


After ensuring that the docker containers are in good state, we will
move on to checking the current state of the OpenStack environment.

The relevant files for this hands-on session can be found under `/opt`

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
