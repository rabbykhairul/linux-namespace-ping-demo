# Ping one linux namespace from another
----

> We'll create 2 namespaces then connect them together with veth cable. After that assign IP address & ping them.

- Create 2 linux network namespaces
```bash
$ sudo ip netns add YOUR_NAMESPACE_1
$ sudo ip netns add YOUR_NAMESPACE_2
```

- Create a veth cable with 2 ends
```bash
$ sudo ip link add YOUR_VETH_INTERFACE_NAME_1 type veth peer name YOUR_VETH_INTERFACE_NAME_2
```

- Connect each interface of the veth cable to one of the network namespaces
```bash
$ sudo ip link set YOUR_VETH_INTERFACE_NAME_1 netns YOUR_NAMESPACE_1
$ sudo ip link set YOUR_VETH_INTERFACE_NAME_2 netns YOUR_NAMESPACE_2
```

- Setup up IP addresses for veth interfaces from each namespace
```bash
$ sudo ip netns exec YOUR_NAMESPACE_1 ip addr add 10.0.0.1/24 dev YOUR_VETH_INTERFACE_NAME_1
$ sudo ip netns exec YOUR_NAMESPACE_2 ip addr add 10.0.0.2/24 dev YOUR_VETH_INTERFACE_NAME_2
```

- Now enable the interfaces connected to each namespace
```bash
$ sudo ip netns exec YOUR_NAMESPACE_1 ip link set dev YOUR_INTERFACE_NAME_1 up
$ sudo ip netns exec YOUR_NAMESPACE_2 ip link set dev YOUR_INTERFACE_NAME_2 up
```

* Now the namespaces are conneted together via the 2 interfaces of the veth cable. We can now ping one namespace from another. Open 2 terminals.

```bash
# From the first terminal
$ sudo ip netns exec YOUR_NAMESPACE_1 ping 10.0.0.2

# From the second terminal
$ sudo ip netns exec YOUR_NAMESPACE_2 ping 10.0.0.1
```
