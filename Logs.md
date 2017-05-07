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
