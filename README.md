### Warning:
There is no input validation. It is assumed your config file is sane.

### Script Overview:

1. **Configuration File Loading**:
    - The script is designed to load a YAML configuration file, which contains various network settings, such as IP addresses, routes, bridges, veth pairs, and routing policies.
    - The default configuration file path is `config/netconfig.yaml`, but this can be overridden by setting the `CONFIG_FILE` environment variable.

2. **Network Configuration Functions**:
    - **`add_addresses()`**:
        - This function reads the configuration for network addresses and adds them to the specified interfaces. The function handles both the assignment of IP addresses and netmasks, and it can also associate interfaces with specific network namespaces.

    - **`add_routes()`**:
        - This function configures network routes as specified in the YAML file. It supports various route attributes such as destination (`to`), gateway (`via`), device (`dev`), routing table (`table`), metric, and whether the route is `onlink`.

    - **`add_routing_policies()`**:
        - This function applies routing policies. It configures rules based on parameters like source and destination (`from`, `to`), table, priority, and specific interfaces (`iif`, `oif`).

    - **`add_bridges()`**:
        - This function is responsible for creating network bridges as defined in the configuration. It reads the bridge name and any associated interfaces that should be added to the bridge. The function can also place bridges within network namespaces.

    - **`add_veths()`**:
        - This function sets up virtual Ethernet (veth) pairs. It creates the veth pairs and optionally associates each end with different namespaces.

    - **`set_interfaces_updown()`**:
        - This function controls the state of interfaces, either bringing them up or down based on the specified configuration.

### Example Configuration File (`netconfig.yaml`):

```yaml
netconfig:
  addresses:
    - interface: "eth0"
      namespace: "ns1"
      address: "192.168.1.10"
      netmask: "255.255.255.0"
    - interface: "eth1"
      namespace: ""
      address: "10.0.0.1"
      netmask: "255.255.255.0"
  
  routes:
    - to: "0.0.0.0/0"
      via: "192.168.1.1"
      dev: "eth0"
      table: "main"
      metric: "100"
      onlink: "true"
      namespace: "ns1"
    - to: "10.0.0.0/8"
      via: "10.0.0.254"
      dev: "eth1"
      namespace: ""

  bridges:
    - name: "br0"
      namespace: "ns1"
      interfaces:
        - name: "eth0"
        - name: "eth1"

  veths:
    - name: "veth0"
      peername: "veth1"
      namespace: "ns1"
      peer_namespace: "ns2"
    - name: "veth2"
      peername: "veth3"
      namespace: ""
      peer_namespace: ""

  routing-policy:
    - from: "192.168.1.0/24"
      table: "1"
      priority: "100"
      namespace: "ns1"
    - from: "10.0.0.0/8"
      table: "2"
      priority: "200"
      namespace: ""
```
