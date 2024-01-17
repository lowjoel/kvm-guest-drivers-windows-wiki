### Introduction to Receive Segment Coalescing (RSC)

RSC is a hardware-based receive acceleration mechanism, reducing CPU load by offloading the task of combining multiple fragments of a large payload to single packet which is then delivered to the OS network stack. When network transfers utilize large packets, this can provide a significant boost in throughput. One benchmark showed a 5x increase (from 7Gbps to 35 Gbps) in host-to-guest data throughput.

RSC is supported by the network stack starting from Windows Server 2012/Windows 8.

See https://technet.microsoft.com/en-us/library/hh997024(v=ws.11).aspx for further details.

### RSC for Virtualized Workloads

RSC is especially effective in virtualized workloads in these circumstances:

* Host-to-guest data transfer or guest-to-guest data transfer inside the same host. In this setup the data comes to guest system via its TAP device. If the transmitting network adapter is able to offload large data segments, those segments can be sent to the guest without fragmentation. This requires the receiving TAP to offload its TX checksumming and TX Large Segment Offload responsibilities.
* Data originating from an external network to the guest. In this case there are two network components responsible for segment coalescing: the receiving physical NIC and the TAP of the guest's network adapter. The first pass of coalescing segments is performed by the NIC (based on its own coalescing setting). Delivery of the segments to the guest is via the TAP and subject to the same considations as the host-to-guest scenario above.

### Requirements for RSC

RSC can be enabled when all of the following are true:

1. On the host:
   1. The TAP device has the TX checksum offload enabled
   2. TCP Segmentation Offload is enabled
2. On the guest:
   1. The virtio driver is installed
   2. RSC is operational within the guest network stack.

#### Host configuration

The host configuration can be verified by running

```bash
ethtool -k <TAP device name>
```

The TAP device name is the QEmu command-line parameter `ifname=<TAP device name>` in `-netdev`.

A working host output would be:

* **tx-checksumming: on**
  * tx-checksum-ipv4: off [fixed]
  * tx-checksum-ip-generic: on
  * tx-checksum-ipv6: off [fixed]
  * tx-checksum-fcoe-crc: off [fixed]
  * tx-checksum-sctp: off [fixed]
* **tcp-segmentation-offload: on**
  * tx-tcp-segmentation: on
  * tx-tcp-ecn-segmentation: off [requested on]
  * tx-tcp6-segmentation: on

Further performance benefit can be gained by performing NIC coalescing:

```bash
ethtool -c <nic name>
```

Typical output:

* **rx-usecs: 3**

This parameter configures the maximum allowed time between packets for the NIC to combine in hardware.

#### Guest configuration

The guest configuration can be verified in PowerShell:

```powershell
Get-NetAdapterRsc | Format-List
```

A working guest output would be:

* Name: Ethernet
* InterfaceDescription: Red Hat VirtIO Ethernet Adapter
* IPv4Enabled: True
* IPv6Enabled: True
* IPv4Supported: True
* IPv6Supported: True
* **IPv4OperationalState: True**
* **IPv6OperationalState: True**
* **IPv4FailureReason: NoFailure**
* **IPv6FailureReason: NoFailure**

Possible failure reason causes:

* Capability: The host has not been configured for RSC.
* WFPCompatibility: check your firewall, try disabling it. The Windows Filtering Platform is preventing RSC from being enabled.

See https://github.com/virtio-win/kvm-guest-drivers-windows/issues/1026 for more details.

### HCK RSC tests

HCK "RSC tests" is currently not part of any playlist.
For engineering purposes in order to pass this specific test and **only for this specific test** use a special addition to virtio-net-pci command line as follows: "**guest_rsc_ext=on,rsc_interval=1000000**". Note that this device parameter is not compatible with any other test as well as with regular netkvm functionality.

