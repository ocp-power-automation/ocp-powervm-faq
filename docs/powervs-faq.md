# PowerVS Frequently Asked Questions

---
## When Building out a Cluster

#### VM Console shows "grub>"
Not the end of the world. Just type `normal` and hit ENTER.

#### VM Console shows "grub rescue>"
GRUB 2 was unable to find the grub folder or its contents are missing/corrupted. The grub folder contains the GRUB 2 menu, modules and stored environmental data. This should not happen very often. Simply reboot the VM from <https://cloud.ibm.com>.

#### RHCOS Ignition stalls with "version mismatch"
The version of RHCOS you are booting up with on cluster nodes cannot handle what the OCP build/version is serving as config for cluster nodes. Make sure they match at least the major and minor versions (eg. 4.5) in your `var.tfvars` file.

#### Error about auth key/token
```text
Error: Error occured while fetching the auth key for power iaas: "Post https://iam.cloud.ibm.com/identity/token: dial tcp: lookup iam.cloud.ibm.com on 192.168.2.1:53: read udp 192.168.2.25:49597->192.168.2.1:53: i/o timeout"
```
You may occasionally get this error upon `terraform apply` runs. One thing you can try is to regenerate IBM Cloud API key and rerun with it. See: <https://cloud.ibm.com/docs/account?topic=account-userapikey>

#### Image Registry not coming up
Toward the end of deploying a cluster or even after your `terraform apply` appears to complete happily, you might find your `image-registry` operator not AVAILABLE. Digging in, if you find the `registry-pvc` PersistentVolumeClaim stuck in Pending state, while `nfs-client-provisioner` pod appears to be Running fine, it may be the case where the backing NFS share is read-only, and thus PVCs can't be fulfilled. Check the directory ownership and permission of `/export` on your bastion node. It has to be owned by "nobody" and world writeable. Use below commands as necessary;
```text
# chown nobody:nobody /export
# chmod 777 /export
# exportfs -r
```
Note: allow a few minutes to sort all things out after these commands.

---
## Problems on a Running Cluster

#### Need to add more CPU/Memory to Node
Go to <https://cloud.ibm.com>, then to your PowerVS service instance, find your VM that backs the cluster Node (eg. myocp-worker-0), click to open up the details page, then "Edit details," update the CPU/Memory with new values, and Save. You should get "Edit successful" message, and your OCP cluster should recognize the change and start showing in Compute section of Web Console. If something fails along the way, try "OS Shutdown" action, edit CPU/Memory as necessary while in "Shutoff" state, and restart the VM.

---
## Securing a Cluster

#### TBA