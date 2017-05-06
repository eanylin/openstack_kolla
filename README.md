# OpenStack Kolla Ansible Hands-On Lab Session

## Create Heat Stack

```
openstack stack create -f yaml --parameter key_name=mykey --parameter image=cirros --parameter flavor=1 --parameter network=demo-net -t heat-test.yaml test_stack1
```
