# GlusterFS-Replica-Cluster-Setup
GlusterFS is a scalable network filesystem suitable for data-intensive tasks like cloud storage and media streaming. This setup utilizes a two-node replica cluster to ensure high availability and data integrity. By mirroring data across multiple bricks, the system remains operational even if one node fails, providing a robust, solution.



---

## 🛠️ Installation (Node 1 & Node 2)

Execute these commands on both servers to install the GlusterFS server and prepare the storage directory.

```bash
# Elevate to root
sudo -i

# Install GlusterFS Server
sudo apt update && sudo apt install glusterfs-server -y

# Enable and start the service
sudo systemctl enable --now glusterd

# Create the brick directory
sudo mkdir -p /gluster/data
```

---

## 🌐 Cluster Configuration (Node 1 Only)

Perform these steps on **Node 1** to establish the peer relationship and initialize the replicated volume. Replace `[name_of_volume]` with your preferred volume name.

1.  **Probe Peer:**
    `gluster peer probe 192.168.1.3`
2.  **Check Status:**
    `gluster peer status`
3.  **Create Replicated Volume:**
    `gluster volume create [name_of_volume] replica 2 192.168.1.2:/gluster/data 192.168.1.3:/gluster/data force`
4.  **Start Volume:**
    `gluster volume start [name_of_volume]`

---

## 📂 Client Mounting & Testing

### **On Node 1**
Mount the volume locally to test write capabilities.

```bash
# Create mount point
mkdir -p /mnt/gluster_client

# Mount the volume
sudo mount -t glusterfs 192.168.1.2:/[name_of_volume] /mnt/gluster_client

# Write test data
echo "This is testing for GlusterFS." | sudo tee /mnt/gluster_client/test.txt
```

---

## 🧪 High Availability Verification

### **Scenario A: Node 2 is Down**
Confirm Node 1 can still access data if Node 2 is destroyed or offline.
* **Action:** Run `cat /mnt/gluster_client/test.txt` on **Node 1**.

### **Scenario B: Node 1 is Down**
Confirm Node 2 can access and mount data locally if Node 1 is offline.

```bash
# On Node 2
cat /gluster/data/test.txt

# Mount locally on Node 2
mkdir -p /mnt/gluster_client
sudo mount -t glusterfs localhost:/[name_of_volume] /mnt/gluster_client
```

---
