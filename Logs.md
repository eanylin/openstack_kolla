## Logs

It is often useful to check the logs when containers are in bad
state or when services are not coming up or working as expected

The logs for various components can be found at the following 
directory.  We can perform further troubleshooting by looking
at the errors messages in these logs.

```
[root@centos _data]# pwd
/var/lib/docker/volumes/kolla_logs/_data
[root@centos _data]# ls
ansible.log  cinder  glance  haproxy  heat  horizon  keystone  libvirt  mariadb  neutron  nova  openvswitch  rabbitmq  swift
[root@centos _data]#
```

The usual docker command can be useful as well.  For instance,
if we want to look at the logs of the cinder-volume container,
we can do the following

```
docker logs $(docker ps -a | grep cinder-volume | awk '{print $1}')
```

As discussed during the workshop, we are using host networking for
the docker containers.  We can check the OVS configurations on the 
Network Node by executing the following command

```
[root@network01 ~]# docker exec $(docker ps -a | grep openvswitch_vswitchd | awk '{print $1}') ovs-vsctl show
```

The usual commands for neutron troubleshooting can be used as
well

```
[root@network01 ~]# ip a
[root@network01 ~]# ip netns ls
[root@network01 ~]# ip netns exec <qrouter-id> ip a
[root@network01 ~]# ip netns exec <qrouter-id> ip r
[root@network01 ~]# ip netns exec <qrouter-id> ping <gateway_ip> -c 3
```

Lastly, we can execute Ansible commands such as the one below
from the **Operator** node if we encountered failures related 
to heat engine.  The command will return output if it finds any
occurence of ***error*** in the heat-engine log files across the 
3 controller nodes.  This is useful for troubleshooting multi-nodes 
environment (***similar concept can be applied to other OpenStack services***)

```
[root@operator opt]# ansible control -i multinode -m shell -a "grep -i error /var/lib/docker/volumes/kolla_logs/_data/heat/heat-engine.log"
```
