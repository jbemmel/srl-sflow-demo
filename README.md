# srl-sflow-demo
Inspired by Peter Phaal's sFlow demo at https://blog.sflow.com/2021/04/containerlab.html this lab creates a leaf-spine CLOS topology using SR Linux containers, fully [configured](https://github.com/jbemmel/srl-sflow-demo/blob/main/leaf1.cfg.json#L1223) to send sFlow records to a standard collector instance (launched as part of the lab).

In SR Linux, sFlow records are sent inband (and not via the management network); the lab uses eBGP to have Spine1 advertise a 172.20.20.0/24 route to its ethernet-1/3 interface which is connected to the sFlow collector (172.20.20.10).

Note: The Containerlab config file uses a [beta](https://github.com/jbemmel/srl-sflow-demo/blob/main/srl-test.yml#L58) feature to connect spine1 e1-3 to the Linux management network bridge, until containerlab release 0.13 comes out a beta binary is included in this repo

## Deploy

`sudo ./beta-clab-mgmt deploy -t ./srl-test.yml`

## sFlow web interface

Open a web browser to http://172.20.20.10:8008/html/index.html to see sFlow stats coming in.
For the lab, only leaf1 is configured to send sFlow UDP packets to the collector:
```
A:leaf1# system sflow                                                                                                                                                                                              
--{ running }--[ system sflow ]--                                                                                                                                                                                  
A:leaf1# info detail                                                                                                                                                                                               
    admin-state enable
    sample-rate 10000
    sample-size 256
    collector 1 {
        collector-address 172.20.20.10
        network-instance default
        source-address 192.168.0.1
        port 6343
    }
```
