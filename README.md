### ClickHouse Cluster Setup with Ansible

This repository contains Ansible playbooks and roles to:
- Set up a ClickHouse cluster across 4 nodes with Zookeeper coordination.
- Configure replicated and distributed tables in the cluster.
- Insert sample data into the distributed table.

---

### **1. Task Overview**

#### **Roles**
1. **`clickhouse_cluster`**: Installs ClickHouse and configures the cluster with Zookeeper.
2. **`clickhouse_tables`**: Creates replicated and distributed tables and inserts data.

#### **Inventory**
Hosts are categorized into `zookeeper_nodes` and `clickhouse_nodes`. Example:
```yaml
all:
  hosts:
    server1:
      ansible_host: 142.93.129.118
    server2:
      ansible_host: 104.248.92.140
    server3:
      ansible_host: 142.93.129.38
    server4:
      ansible_host: 104.248.92.142
  children:
    zookeeper_nodes:
      hosts:
        server1:
        server2:
        server3:
    clickhouse_nodes:
      hosts:
        server1:
        server2:
        server3:
        server4:
```

---

### **2. How to Run**

#### **Prerequisites**
- Ansible installed on the control machine.
- SSH access to all target nodes.
- Sudo privileges on target nodes.

#### **Run the Playbooks**

 **Install ClickHouse and Configure the Cluster and configure tables:**
   ```bash
   ansible-playbook -i inventory.yaml clickhouse.yml
   ```

---

### **3. Files and Directories**

- **`roles/clickhouse_cluster/`**:
  - Installs and configures ClickHouse and Zookeeper.
  - Configures cluster files (`config.xml`, `cluster.xml`).

- **`roles/clickhouse_tables/`**:
  - Creates `events_local` and `events_distributed` tables.
  - Inserts sample data.

---

### **4. Example Commands and Outputs**

#### **Check Cluster Status:**
```bash
clickhouse-client --query "SELECT * FROM system.clusters"
```

#### **Verify Data in `events_distributed`:**
```bash
clickhouse-client --query "SELECT * FROM events_distributed"
```
