# testproject

## Ubuntu pre bootstrap

Ubuntu 18.04 has default user and password `ubuntu`. The first login requires
password change. Ansible does not expect that and fails to connect.
To fix that, login manually and change password to `raspberry1`.

## [Kubernetes network plugin][11]

### [Calico][9]

The first attempt was based on calico.

```
curl -OL https://docs.projectcalico.org/v3.7/manifests/calico.yaml
sed s/v3.7.2/v3.7.2-arm64/ calico.yaml > calico-arm64.yaml
kubectl apply -f calico-arm64.yaml
```

For some reason calico pods were crashing:

```
eduard@master1:~$ kubectl get pod -n kube-system
NAME                                         READY   STATUS              RESTARTS   AGE
calico-kube-controllers-55b489fd78-qxsbw     0/1     Error               0          8m8s
calico-node-74ls7                            0/1     CrashLoopBackOff    5          8m8s
calico-node-hch8l                            0/1     CrashLoopBackOff    5          8m8s
calico-node-m4j6d                            0/1     CrashLoopBackOff    2          8m8s
coredns-fb8b8dccf-hjsn5                      0/1     ContainerCreating   0          7h3m
coredns-fb8b8dccf-kmht5                      0/1     ContainerCreating   0          7h3m
etcd-master1.kube.local                      1/1     Running             0          7h3m
kube-apiserver-master1.kube.local            1/1     Running             0          7h2m
kube-controller-manager-master1.kube.local   1/1     Running             4          7h3m
kube-proxy-6pfz7                             1/1     Running             0          6h20m
kube-proxy-ncjqb                             1/1     Running             0          6h20m
kube-proxy-ptt85                             1/1     Running             0          7h3m
kube-scheduler-master1.kube.local            1/1     Running             4          7h3m
```

### [Flannel][10]

Flannel is the simplest network plugin for kubernetes. It worked fine for Raspberry Pi.

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.11.0/Documentation/kube-flannel.yml
```

## Expose to the Internet

### Dynamic DNS on Mikrotik

### Load balancer on Mikrotik

Load balancer implementation will use [Per Connection Classifier][14] and [Port Forwarding(DST NAT)][15].

```
/ip firewall mangle
add chain=prerouting action=mark-connection \
  in-interface=all-ppp protocol=tcp dst-port=80 \
  new-connection-mark=kube_node_1 per-connection-classifier=src-address-and-port:2/0 \
  comment="Load balancer. Service nginx. Mark connections to kube node 1"
add chain=prerouting action=mark-connection \
  in-interface=all-ppp protocol=tcp dst-port=80 \
  new-connection-mark=kube_node_2 per-connection-classifier=src-address-and-port:2/1 \
  comment="Load balancer. Service nginx. Mark connections to kube node 2"

/ip firewall nat
add chain=dstnat action=dst-nat \
 connection-mark=kube_node_1 to-addresses=192.168.40.103 protocol=tcp to-ports=30560 \
 comment="Load balancer. Service nginx. DST NAT to kube node 1"
add chain=dstnat action=dst-nat \
  connection-mark=kube_node_2 to-addresses=192.168.40.104 protocol=tcp to-ports=30560 \
  comment="Load balancer. Service nginx. DST NAT to kube node 2"
```

[1]: https://wiki.ubuntu.com/ARM/RaspberryPi
[2]: https://wiki.mikrotik.com/wiki/Manual:Basic_VLAN_switching#Other_devices_with_built-in_switch_chip
[3]: https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04
[4]: https://www.projectcalico.org/announcing-calico-v3-2/
[5]: https://medium.com/nycdev/k8s-on-pi-9cc14843d43
[6]: https://www.raspberrypi.org/forums/viewtopic.php?t=203128
[7]: https://bugs.launchpad.net/ubuntu/+source/linux-signed/+bug/1771859
[8]: https://wiki.ubuntu.com/ARM/RaspberryPi
[9]: https://www.projectcalico.org/
[10]: https://github.com/coreos/flannel
[11]: https://kubernetes.io/docs/concepts/cluster-administration/networking/
[13]: https://wiki.mikrotik.com/wiki/Load_Balancing
[14]: https://wiki.mikrotik.com/wiki/Manual:PCC
[15]: https://wiki.mikrotik.com/wiki/Manual:IP/Firewall/NAT
