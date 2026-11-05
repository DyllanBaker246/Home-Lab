# This is the network config written by 'subiquity'
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 192.168.137.7/24
      routes:
        - to: default
          via: 192.168.137.1
      nameservers:
        addresses:
          - 1.1.1.1

