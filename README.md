Configure the openstack environment.  Things like creating security groups, networks, etc.

To deploy an instance, you can add the following to your group_vars/all.yaml:

```
  servers:
    - server_name: test-server
        lock_state: 'lock'
        image: Ubuntu-16.04
        keyname: 'keypair'
        flavor: 100 
        network: TESTNET
        auto_ip: yes 
        security_groups: [ 'default' ]
        meta: 'groups=test'
        userdata: |
          #cloud-config
          packages:
            - python-minimal  # Ubuntu-16.04 needs this for ansible
```

To create/manage a security groups:

```
  os_security_groups:
    - name: 'default'
      description: 'Default rules - effects all VMs'
      rule:
        - port_range_min: 22
          remote_ip_prefix: '0.0.0.0/0'
        - port_range_min: 80
          remote_ip_prefix: '10.10.10.0/24'
        - port_range_min: 0
          protocol: 'icmp'
          remote_ip_prefix: '0.0.0.0/0'
```

To create networks:

```
  os_networks:
    - network_name: 'TESTNET'
      subnets:
        - subnet_name: 'subnet1'
          cidr: '10.10.10.0/24'
          dns_nameservers:
            - '8.8.8.8'
            - '8.8.4.4'
```

To create routers:

```
  os_routers:
    - name: 'TESTROUTER'
      interfaces:
        - TESTNET
```

Keypairs are taking from the user variable, which is often used for system
accounts as well. As such, just make sure your user has a "key" attribute and 
that will be used to create the ssh key for the user:

```
  users:
    - name: testymctester
      key: 'ssh-rsa AAAAB3NzaC1yc2EAAAAhAQABAAABAQhx7EsGj/e47ZWrORt5vz+gfj9thxBu7xgoRd+5vevGRcDhPFLgVUaQjBl2hURjsIPCLRJTA+mmnhzRI+IRqiRhiYBHmRhfUh9UwDF/rkFd4hRU/YkCKQtxRhlXEqLR41GWbhN3/OsIYARChp00E6FJw8hJ6V4pDatwRSr701+Lqg7qfhzF+iLJS4dRLY6hLMTgM5t5wshIhYR5zxvPDL4CPhom1vz0LFqRKjhNfsD6NGR48chePM2j52jQrMXhmRbdOIxRzTtFLkZqhaUCaiPHQkwh270ejheRjfclLh7eIUVwN/QRb8vMRhi5D7u0RbPharXYRsdft7VbI5ph+zXi025frZfR'
```
