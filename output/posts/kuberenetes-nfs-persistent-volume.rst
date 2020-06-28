.. title: Kuberenetes NFS persistent volume
.. slug: kuberenetes-nfs-persistent-volume
.. date: 2020-06-18 14:30:13 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

k8s_nfs_persistent_volume

# Create nfs persistent volume:

##What you need

NFS Server I have used QNAP NAS

Persistent volume
—-
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0001
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteMany
  mountOptions:
    - nfsvers=4.1
  nfs:
    path: /PersistentVolume
    server: 192.168.1.11
  persistentVolumeReclaimPolicy: Retain
—-

Volume claim
—-
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteMany 
  resources:
    requests:
      storage: 5Gi 
—-


