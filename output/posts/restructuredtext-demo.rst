.. title: ReStructuredText Demo
.. slug: restructuredtext-demo
.. date: 2020-06-18 13:49:29 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

k8s-volumes

#NFS Volumes
create NFS share on my QNAP with squash option no_root_squash.




#Local volumes
Didnt manage to launch

Change nodes ip 
sudo vim /var/lib/kubelet/kubeadm-flags.env
--node-ip=192.168.50.12
KUBELET_KUBEADM_ARGS="--cgroup-driver=cgroupfs --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.2 --resolv-conf=/run/systemd/resolve/resolv.conf --node-ip=192.168.50.12"

systemctl restart kubelet

#exec
 kubectl exec --stdin --tty mysql-deployment-bfcd64b55-mqp4q -- /bin/bash

#sharing vol between pods
My working solutuion not advised:

apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          ports:
            - containerPort: 3306
          volumeMounts:
            - mountPath: "/var/lib/mysql"
              subPath: "mysql"
              name: mysql-data
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secrets
                  key: ROOT_PASSWORD
      volumes:
        - name: mysql-data
          persistentVolumeClaim:
            claimName: task-pv-claim




#sharing dir between pods

First of all. Kubernetes doesn't have integrated functionality to share storage between hosts. There are several options below. But first how to share storage if you already have some volumes set up.

To share a volume between multiple pods you'd need to create a PVC with access mode ReadWriteMany

kind: PersistentVolumeClaim
apiVersion: v1
metadata:
    name: my-pvc
spec:
    accessModes:
      - ReadWriteMany
    storageClassName: myvolume
    resources:
        requests:
            storage: 1Gi
After that you can mount it to multiple pods:

apiVersion: v1
kind: Pod
metadata:
  name: myapp1
spec:
  containers:
...
      volumeMounts:
        - mountPath: /data
          name: data
          subPath: app1
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: 'my-pvc'
---
apiVersion: v1
kind: Pod
metadata:
  name: myapp2
spec:
  containers:
...
      volumeMounts:
        - mountPath: /data
          name: data
          subPath: app2
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: 'my-pvc'
Of course, persistent volume must be accessible via network. Otherwise you'd need to make sure that all the pods are scheduled to the node with that volume.

There are several volume types that are suitable for that and not tied to any cloud provider:

NFS
RBD (Ceph Block Device)
CephFS
Glusterfs
Portworx Volumes
Of course, to use a volume you need to have it first. That is, if you want to consume NFS you need to setup NFS on all nodes in K8s cluster. If you want to consume Ceph, you need to setup Ceph cluster and so on.

The only volume type that supports Kubernetes out of the box is Portworks. There are instruction on how to set it up in GKE.

To setup Ceph cluster in K8s there's a project in development called Rook.

But this is all overkill if you just want a folder from one node to be available in another node. In this case just setup NFS server. It wouldn't be harder than provisioning other volume types and will consume much less cpu/memory/disk resources.
## Example1 Configure a Pod to Use a PersistentVolume for Storage

https://kubernetes.io/blog/2018/04/13/local-persistent-volumes-beta/

apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv-volume
  labels:
    type: local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /mnt/data
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - node

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  storageClassName: local-storage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
