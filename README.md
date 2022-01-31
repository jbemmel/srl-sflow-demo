# srl-sflow-demo
Inspired by Peter Phaal's sFlow demo at https://blog.sflow.com/2021/04/containerlab.html this lab creates a leaf-spine CLOS topology using SR Linux containers, fully [configured](https://github.com/jbemmel/srl-sflow-demo/blob/main/leaf1.cfg.json#L1224) to send sFlow records to a standard collector instance (launched as part of the lab).

In SR Linux, sFlow records are sent inband (and not via the management network); the lab uses eBGP to have Spine1 advertise a 172.20.20.0/24 route to its ethernet-1/3 interface which is connected to the sFlow collector (172.20.20.10).

Note: The Containerlab config file uses a [0.13 feature](https://containerlab.srlinux.dev/manual/network/#additional-connections-to-management-network) [here](https://github.com/jbemmel/srl-sflow-demo/blob/main/srl-test.yml#L50) to connect spine1 e1-3 to the Linux management network bridge

## Deploy

```
sudo containerlab deploy -t ./srl-test.yml
```

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

## SR Linux sFlow documentation
Can be found here: https://infocenter.nokia.com/public/SRLINUX213R1A/index.jsp?topic=%2Fcom.srlinux.toolkit%2Fhtml%2Ftoolkit-sflow.html

## DDoS detection
As explained in https://www.sanog.org/resources/sanog37/SANOG37_Conference-Network_Telemetry_for_DDoS_Detection_Applications-Pavel_Odintsov-FastNetMon_Project.pdf sFlow can be used as input for DDoS detection tools. This works best with hardware based sFlow (like on 7250 IXR), with reasonable sampling rates

On 7250 IXR:
```
--{ + candidate shared default }--[ system sflow ]--
A:leaf1# sample-rate <value:10000>
usage: sample-rate <value>

Specify sFlow sample rate

This value is the rate at which traffic will be sampled at a rate of 1:N received packets.

Positional arguments:
  value             [number, range 1..2000000, default 10000]
```

