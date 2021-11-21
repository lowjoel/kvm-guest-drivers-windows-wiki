**[netkvm-wmi.cmd](https://github.com/virtio-win/kvm-guest-drivers-windows/blob/master/NetKVM/DebugTools/WMI/netkvm-wmi.cmd)** is a simple developer-oriented tool that allows access to NetKVM driver to query or control the behavior of the driver.

It is based on WMI interface of the network driver: WMI commands are converted to custom OID that the OS sends to the NetKVM driver.

In case of multiple NetKVM devices (multiple network adapters) each command of the netkvm-wmi.cmd is executed for all the devices.

Examples of most useful commands are:
* netkvm-wmi.cmd debug 1: sets logging level of the driver to 1 at runtime
* netkvm-wmi.cmd stat: returns run-time statistics of the adapters
* netkvm-wmi.cmd rss: returns run-time RSS statistics of the adapters (and resets it)
* netkvm-wmi.cmd qfo: returns status of NetKVM failover feature of the adapters

For all the available commands run **[netkvm-wmi.cmd](https://github.com/virtio-win/kvm-guest-drivers-windows/blob/master/NetKVM/DebugTools/WMI/netkvm-wmi.cmd)** without parameters.

To add new commands for development/testing purposes see the implementation of existing WMI commands:
* https://github.com/virtio-win/kvm-guest-drivers-windows/blob/master/NetKVM/Common/netkvm.mof
* vendor-specific commands in https://github.com/virtio-win/kvm-guest-drivers-windows/blob/master/NetKVM/wlh/ParaNdis6_Oid.cpp


