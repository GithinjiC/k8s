### Volumes
*Look into the different types of volumes.*  
*Persistent Volume is cluster-wide.*  
*PersistentVolume object has three accessModes: ReadOnlyMany, ReadWriteOnce, ReadWriteMany.*  
*`persistentVolumeReclaimPolicy` can be Retail or Delete. What happens to a PV when a PVC is deleted. The pvc is deleted once the pod is deleted and the PV goes to Released state.*  
*pv and pvc accessModes must match.*  

### Storage Class
*Dynamic Provisioning of storage. PV is created automatically by the storage class. Each provider has different parameters.*  
*Look into Provisioners and volumebinding modes*
