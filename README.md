Here you go — the final **`README.md`** file content ready to save:

````markdown
# Elasticsearch 3-Node Cluster with Vagrant + VirtualBox

This project provisions a **multi-node Elasticsearch 8.x cluster** using **Vagrant** and **VirtualBox**.  
Each VM runs Ubuntu 22.04 (Jammy) and is configured as an Elasticsearch node with discovery and clustering enabled.

---

## 📦 Cluster Setup

- **Base Box**: `ubuntu/jammy64`
- **Default Nodes**:
  - `esnode1` → `192.168.56.11`
  - `esnode2` → `192.168.56.12`
  - `esnode3` → `192.168.56.13`
- **Resources per node**:
  - 2 CPUs
  - 2GB RAM
- **Cluster name**: `dev-es-cluster`

---

## ⚡ Prerequisites

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Vagrant](https://developer.hashicorp.com/vagrant/downloads)

Verify installation:
```bash
vagrant -v
virtualbox --help
````

---

## 🚀 Usage

### 1. Start the cluster

```bash
vagrant up
```

This will:

* Boot 3 VMs
* Install Java & Elasticsearch
* Configure `elasticsearch.yml`
* Start nodes in sequence (`esnode1` → `esnode2` → `esnode3`)

### 2. SSH into a node

```bash
vagrant ssh esnode1
```

### 3. Check cluster health

From **esnode3** (or any node):

```bash
curl -X GET "localhost:9200/_cluster/health?pretty"
```

Check nodes:

```bash
curl -X GET "localhost:9200/_cat/nodes?v"
```

---

## 🔧 Important Configuration

`/etc/elasticsearch/elasticsearch.yml` contains:

```yaml
cluster.name: dev-es-cluster
node.name: esnode1  # (or esnode2 / esnode3)
network.host: 0.0.0.0
transport.host: 192.168.56.xx
http.port: 9200
transport.port: 9300

discovery.seed_hosts: ["192.168.56.11:9300", "192.168.56.12:9300", "192.168.56.13:9300"]
cluster.initial_master_nodes: ["esnode1", "esnode2", "esnode3"]

xpack.security.enabled: false
```

⚠️ **Note**:
`cluster.initial_master_nodes` is only needed for the **first cluster bootstrap**. After the cluster has formed once, you should remove or comment this line from all nodes before restarting them.

---

## ➕ Scaling the Cluster

You can easily add more nodes by editing the `nodes` list in your `Vagrantfile`.

For example, to add a **4th node**:

```ruby
nodes = [
  { name: "esnode1", ip: "192.168.56.11" },
  { name: "esnode2", ip: "192.168.56.12" },
  { name: "esnode3", ip: "192.168.56.13" },
  { name: "esnode4", ip: "192.168.56.14" }  # NEW NODE
]
```

Then start only the new VM:

```bash
vagrant up esnode4
```

From inside the new node, ensure `elasticsearch.yml` has:

```yaml
node.name: esnode4
discovery.seed_hosts: ["192.168.56.11:9300", "192.168.56.12:9300", "192.168.56.13:9300"]
```

Check if it joined:

```bash
curl -X GET "localhost:9200/_cat/nodes?v"
```

---

## 🛑 Teardown

To destroy all VMs:

```bash
vagrant destroy -f
```

To restart from scratch:

```bash
vagrant up --provision
```

---

## 📝 Troubleshooting

* **Master not discovered error (`503`)**
  Ensure `cluster.initial_master_nodes` matches the **actual `node.name` values**:

  ```yaml
  cluster.initial_master_nodes: ["esnode1", "esnode2", "esnode3"]
  ```

* **Check logs**:

  ```bash
  journalctl -u elasticsearch -f
  ```

* **Verify Java**:

  ```bash
  java -version
  ```

---

## ✅ Next Steps

* Connect Kibana to the cluster
* Add monitoring with Metricbeat
* Secure cluster with TLS and built-in users (optional)

---

### Author

Created for testing a lightweight **multi-node Elasticsearch cluster** using Vagrant + VirtualBox.

```

---

👉 Do you want me to also create a **separate `Vagrantfile.example` for 4 nodes**, so you can just copy-paste instead of editing the 3-node one?
```
