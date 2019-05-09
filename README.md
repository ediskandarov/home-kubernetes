# testproject

## Ubuntu pre bootstrap

Ubuntu 18.04 has default user and password `ubuntu`. The first login requires
password change. Ansible does not expect that and fails to connect.
To fix that, login manually and change password to `raspberry1`.


[1]: https://wiki.ubuntu.com/ARM/RaspberryPi
[2]: https://wiki.mikrotik.com/wiki/Manual:Basic_VLAN_switching#Other_devices_with_built-in_switch_chip
