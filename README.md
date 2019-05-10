# testproject

## Ubuntu pre bootstrap

Ubuntu 18.04 has default user and password `ubuntu`. The first login requires
password change. Ansible does not expect that and fails to connect.
To fix that, login manually and change password to `raspberry1`.


[1]: https://wiki.ubuntu.com/ARM/RaspberryPi
[2]: https://wiki.mikrotik.com/wiki/Manual:Basic_VLAN_switching#Other_devices_with_built-in_switch_chip
[3]: https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04
[4]: https://www.projectcalico.org/announcing-calico-v3-2/
[5]: https://medium.com/nycdev/k8s-on-pi-9cc14843d43
[6]: https://www.raspberrypi.org/forums/viewtopic.php?t=203128
[7]: https://bugs.launchpad.net/ubuntu/+source/linux-signed/+bug/1771859
[8]: https://wiki.ubuntu.com/ARM/RaspberryPi
