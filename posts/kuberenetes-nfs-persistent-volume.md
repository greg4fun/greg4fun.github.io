.. title: Kuberenetes NFS persistent volume
.. slug: kuberenetes-nfs-persistent-volume
.. date: 2020-06-18 18:30:13 UTC
.. tags: kubernetes, k8s, docker, persistent volume,
.. category: 
.. link: 
.. description: 
.. type: text

k8s_nfs_persistent_volume

# Create nfs persistent volume:

##What you need

* NFS Server I have used NFS already installed on my QNAP NAS (You need to enable NO_ROOT_SQUASH on permissions)

* K8s cluster


Now having your NFS share here 192.168.1.11/Persistentvolume/
you can try if it works with mount 

    sudo mount -t nfs 192.168.1.11:/PersistentVolume /mnt/PersistentVolume

Later on you can secure access with password.

If everything works fine we need persistent volume on our cluster

persistentvolume.yml

    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: pv0001
    spec:
      capacity:
        storage: 100Gi
      accessModes:
      - ReadWriteMany
      mountOptions:
        - nfsvers=4.1
      nfs:
        path: /PersistentVolume/pv0001
        server: 192.168.1.11
      persistentVolumeReclaimPolicy: Retain

Apply above yaml to the cluster

    kubectl apply -f persistentvolume.yml

Now we need to declare persistent volume claim 

persistentvolumeclaim.yml

    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      accessModes:
        - ReadWriteMany 
      resources:
        requests:
          storage: 10Gi 

Apply

    kubectl apply -f persistentvolumeclaim.yml


Check if it has been bound:

    kubectl get pv

    NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
    pv0001   100Gi      RWX            Retain           Bound    default/mysql-pv-claim                           2d4h

    kubectl get pvc 
    NAME              STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    mysql-pv-claim    Bound     pv0001   100Gi      RWX                           2d4h


