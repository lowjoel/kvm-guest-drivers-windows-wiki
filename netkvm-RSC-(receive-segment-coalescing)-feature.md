### RSC explanation
See also https://technet.microsoft.com/en-us/library/hh997024(v=ws.11).aspx

RSC is hardware-based receive acceleration machanism, reducing CPU load by offloading task of combining multiple fragments of large payload to single packet which is delivered to OS network stack. When network transfer utilize large packet, this feature can provide significant throughput boost (as an example, change from 7Gbps to 35 Gbps in host-to-guest data transfer was measured in throughput tests).

RSC is supported by network driver starting from 2012 server (or Win8 client). However, it will function only if allowed by setting of host network device responsible to data transfer to guest system (usually TAP device).

### RSC-related settings
There are 2 scenarios when RSC can improve network performance:
* Host-to-guest data transfer or guest-to-guest data transfer inside the same host. In this setup the data comes to guest system via its TAP device. If transmitting network adapter is able to offload large data segments, they can be moved to receiving side without being fragmented. This requires receiving TAP to enable its TX checksumming and TX LSO capability.
* Data coming from external network to guest system. In this case there are two network components responsible for segments coalescing: receiving physical NIC and TAP of guest network adapter. Actual coalescing job is executed by the NIC (based on its coalescing setting) and further delivery to guest system is executed via the TAP and requires the same TX checksumming and TX LSO to be enabled.

### Examples:
**Query TAP offload configuration**

`ethtool -k <tap name>`

Note: tap name is the parameter passed in qemu command-line as `ifname=<name>` in `-netdev` configuration for the tap

Typical output:
* **tx-checksumming: on**
*	tx-checksum-ipv4: off [fixed]
*	tx-checksum-ip-generic: on
*	tx-checksum-ipv6: off [fixed]
*	tx-checksum-fcoe-crc: off [fixed]
*	tx-checksum-sctp: off [fixed]
* **tcp-segmentation-offload: on**
*	tx-tcp-segmentation: on
*	tx-tcp-ecn-segmentation: off [requested on]
*	tx-tcp6-segmentation: on

**Query NIC coalescing configuration**

`ethtool -c <nic name>`

Typical output:
* rx-usecs: 3 (This parameter sets maximal time for NIC combine received fragments into large one)