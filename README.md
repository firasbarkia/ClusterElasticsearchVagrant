# Elasticsearch 3-Node Cluster with Vagrant & VirtualBox

This project provisions a **3-node Elasticsearch cluster** using **Vagrant** and **VirtualBox**.  
It automatically installs Elasticsearch 8.x on Ubuntu 22.04 (`ubuntu/jammy64`), configures cluster settings, and starts the services.

---

## 🚀 Requirements
- [Vagrant](https://developer.hashicorp.com/vagrant/downloads) (>= 2.3)
- [VirtualBox](https://www.virtualbox.org/) (>= 6.x)
- At least **6 GB RAM** free (each node uses 2 GB)
- Internet access (to download packages and Elasticsearch repo)

---

## 📦 Cluster Setup

| Node     | Hostname | IP Address      | Roles                   |
|----------|----------|-----------------|-------------------------|
| esnode1  | esnode1  | `192.168.56.11` | master, data, ingest    |
| esnode2  | esnode2  | `192.168.56.12` | master, data, ingest    |
| esnode3  | esnode3  | `192.168.56.13` | master, data, ingest    |

---

## ⚙️ Installation & Usage

### 1. Clone this repository
```bash
git clone https://github.com/your-repo/elasticsearch-vagrant.git
cd elasticsearch-vagrant
```

### 2. Start the cluster
```bash
vagrant up
```

This will:
- Create 3 VMs (`esnode1`, `esnode2`, `esnode3`)
- Install Java & Elasticsearch
- Configure cluster discovery
- Start nodes in sequence

### 3. Verify cluster health
SSH into the last node (`esnode3`) and run:
```bash
vagrant ssh esnode3
curl -X GET "localhost:9200/_cluster/health?pretty"
curl -X GET "localhost:9200/_cat/nodes?v"
```

You should see **3 nodes** and cluster status as `green` or `yellow`.

---

## 🛠️ Troubleshooting

- If you see **`master_not_discovered_exception`**:
  - Ensure node names in `elasticsearch.yml` match:
    ```yaml
    cluster.initial_master_nodes: ["esnode1", "esnode2", "esnode3"]
    ```
  - Restart services:
    ```bash
    sudo systemctl restart elasticsearch
    ```
- Check logs:
  ```bash
  journalctl -u elasticsearch -f
  ```

---

## 📌 Notes
- Security (`xpack.security.*`) is disabled for simplicity.
- Default heap size is set to **512 MB** (edit `custom-heap.options` to adjust).
- This setup is for **development/testing only**, not production.
- `cluster.initial_master_nodes` should only be set on the **first cluster startup**. Once the cluster has formed, you can remove it from configs.

---

## 🧹 Cleanup
To remove the cluster:
```bash
vagrant destroy -f
```

---

## 📚 References
- [Elasticsearch Docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
- [Vagrant Documentation](https://developer.hashicorp.com/vagrant/docs)
