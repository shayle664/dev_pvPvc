# K3s + NFS + Nginx Deployment (Practice Project)

This project demonstrates how to deploy an Nginx web server with 3 replicas on a K3s cluster using an NFS volume for shared storage.

## 📁 Folder Structure

Place the following files in the root of your project:

- `my_pv.yaml`
- `my_pvc.yaml`
- `deploymentPvPvc.yaml`

## 📂 NFS Setup (on the K3s Server)

1. **Install NFS Server** (Ubuntu):
   ```bash
   sudo apt update
   sudo apt install nfs-kernel-server -y
   ```
   
2. **Create NFS shared folder**:
   ```bash
   sudo mkdir -p /srv/nfs/k3s-data
   sudo chown nobody:nogroup /srv/nfs/k3s-data
   sudo chmod 777 /srv/nfs/k3s-data
   ```

3. **Configure NFS exports**:
Edit /etc/exports file and add:
```bash 
/srv/nfs/k3s-data *(rw,sync,no_subtree_check,no_root_squash)
#Shares the folder with all clients, gives read-write access, 
#disables subtree check, and keeps root permissions (required for containers).
```

4. **Apply the export**:
   ```bash
   sudo exportfs -a
   sudo systemctl restart nfs-kernel-server
   ```
   
5. **Create Kubernetes Setup**:

   1. Clone this repo.
   
   2. Add Index.html to PV:
      ```bash
	  cp index.html /srv/nfs/k3s-data/
	  ```
   
   3. Create PersistentVolume:
      ```bash
	  kubectl apply -f nfs-pv.yaml
	  ```
	  
   4. Create PersistentVolumeClaim:
      ```bash
	  kubectl apply -f nfs-pvc.yaml
	  ```
	  Verify it's bound:
	  ```bash
	  kubectl get pvc
      ```
	  
   5. Deploy Nginx:
      ```bash
	  kubectl apply -f nginx-deployment.yaml
      ```