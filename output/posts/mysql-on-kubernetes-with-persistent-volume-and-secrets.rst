.. title: Mysql on kubernetes with persistent volume and secrets
.. slug: mysql-on-kubernetes-with-persistent-volume-and-secrets
.. date: 2020-06-28 08:50:51 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Volumes
=======

Persistent storage with NFS
---------------------------

In this example I have created nfs share "PersistentVolume" on my qnap NAS which IP is 192.168.1.11
Create persistentVolume.yml


.. code-block:: yaml

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


Create persistentVolumeClaim.yml

.. code-block:: yaml

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


Secrets
-------

The configuration of your containers should be stored in separate place to guarantee mobility (it shouldnt be hardcoded ) neither should it be stored in database
The best approach is to store your configuration in environment variables for docker for instance you can store it in env files which are gitignored or env vars which you need to set during container startup.
In kubernetes you have option to store all configuration like usernames, passwords, API urls etc in configmaps and secrets.
Passwords shouldnt be stored in configmaps though as it is stored there in plain text.So the best choice for passwords is secrets which stores data in base64.


Create password and user and db name and encode it with base64

.. code-block:: bash

    echo -n "MyPassword" | base64 #TXlQYXNzd29yZA==
    echo -n "django" | base64  # ZGphbmdv
    echo -n "kubernetes_test" | base64 # a3ViZXJuZXRlc190ZXN0

Apply above results to secret.yml

.. code-block:: yaml

    ---
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysql-secrets
    type: Opaque
    data:
      MYSQL_ROOT_PASSWORD: TXlQYXNzd29yZA==
      MYSQL_USER: ZGphbmdv
      MYSQL_PASSWORD: ZGphbmdv
      MYSQL_DATABASE: a3ViZXJuZXRlc190ZXN0

On your cluster create secrets.yml


.. code-block:: bash

    kubectl create -f secrets.yml

Mysql application
-----------------

Now having persistent volumeclain and secrets we can write mysql deployment file

deployment.yml

.. code-block:: yaml

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
                      key: MYSQL_ROOT_PASSWORD
                - name: MYSQL_USER
                  valueFrom:
                    secretKeyRef:
                      name: mysql-secrets
                      key: MYSQL_USER
                - name: MYSQL_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: mysql-secrets
                      key: MYSQL_PASSWORD
                - name: MYSQL_DATABASE
                  valueFrom:
                    secretKeyRef:
                      name: mysql-secrets
                      key: MYSQL_DATABASE
          volumes:
            - name: mysql-data
              persistentVolumeClaim:
                claimName: mysql-pv-claim


.. code-block:: bash
    
    kubectl apply -f deployment.yml

Checking
--------

Now we can check if our deployment was successful:

.. code-block:: bash

    kubectl get deployments
    
    NAME               READY   UP-TO-DATE   AVAILABLE   AGE
    mysql-deployment   1/1     1            1           66m

If somethings wrong you can always investigate with describe or logs

.. code-block:: bash

    kubectl describe deployment mysql-deployment

    Name:                   mysql-deployment
    Namespace:              default
    CreationTimestamp:      Sun, 28 Jun 2020 17:02:00 +0000
    Labels:                 app=mysql
    Annotations:            deployment.kubernetes.io/revision: 1
    Selector:               app=mysql
    Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:           RollingUpdate
    MinReadySeconds:        0
    RollingUpdateStrategy:  25% max unavailable, 25% max surge
    Pod Template:
    Labels:  app=mysql
    Containers:
    mysql:
        Image:      mysql:5.7
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
        MYSQL_ROOT_PASSWORD:  <set to the key 'MYSQL_ROOT_PASSWORD' in secret 'mysql-secrets'>  Optional: false
        MYSQL_USER:           <set to the key 'MYSQL_USER' in secret 'mysql-secrets'>           Optional: false
        MYSQL_PASSWORD:       <set to the key 'MYSQL_PASSWORD' in secret 'mysql-secrets'>       Optional: false
        MYSQL_DATABASE:       <set to the key 'MYSQL_DATABASE' in secret 'mysql-secrets'>       Optional: false
        Mounts:
        /var/lib/mysql from mysql-data (rw,path="mysql")
    Volumes:
    mysql-data:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
    Type           Status  Reason
    ----           ------  ------
    Available      True    MinimumReplicasAvailable
    Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-deployment-579b8bb767 (1/1 replicas created)
    Events:          <none>

Or investigate pods

.. code-block:: bash

    kubectl get pods

    NAME                                READY   STATUS    RESTARTS   AGE
    mysql-deployment-579b8bb767-mk5jx   1/1     Running   0          69m

    kubectl describe pod mysql-deployment-579b8bb767-mk5jx 

    Name:         mysql-deployment-579b8bb767-mk5jx
    Namespace:    default
    Priority:     0
    Node:         worker4/192.168.50.15
    Start Time:   Sun, 28 Jun 2020 17:02:00 +0000
    Labels:       app=mysql
                pod-template-hash=579b8bb767
    Annotations:  cni.projectcalico.org/podIP: 192.168.199.131/32
    Status:       Running
    IP:           192.168.199.131
    IPs:
    IP:           192.168.199.131
    Controlled By:  ReplicaSet/mysql-deployment-579b8bb767
    Containers:
    mysql:
        Container ID:   docker://b755c731e9b72812040d62315a2499d05cdaa6b8425e6b357fa19f1e9d6aed2c
        Image:          mysql:5.7
        Image ID:       docker-pullable://mysql@sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
        Port:           3306/TCP
        Host Port:      0/TCP
        State:          Running
        Started:      Sun, 28 Jun 2020 17:02:02 +0000
        Ready:          True
        Restart Count:  0
        Environment:
        MYSQL_ROOT_PASSWORD:  <set to the key 'MYSQL_ROOT_PASSWORD' in secret 'mysql-secrets'>  Optional: false
        MYSQL_USER:           <set to the key 'MYSQL_USER' in secret 'mysql-secrets'>           Optional: false
        MYSQL_PASSWORD:       <set to the key 'MYSQL_PASSWORD' in secret 'mysql-secrets'>       Optional: false
        MYSQL_DATABASE:       <set to the key 'MYSQL_DATABASE' in secret 'mysql-secrets'>       Optional: false
        Mounts:
        /var/lib/mysql from mysql-data (rw,path="mysql")
        /var/run/secrets/kubernetes.io/serviceaccount from default-token-4wtnw (ro)
    Conditions:
    Type              Status
    Initialized       True 
    Ready             True 
    ContainersReady   True 
    PodScheduled      True 
    Volumes:
    mysql-data:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    default-token-4wtnw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-4wtnw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
    Events:          <none>

Or logs from pod


.. code-block:: bash

    kubectl logs mysql-deployment-579b8bb767-mk5jx

    2020-06-28T17:02:13.695295Z 0 [Note] IPv6 is available.
    2020-06-28T17:02:13.695350Z 0 [Note]   - '::' resolves to '::';
    2020-06-28T17:02:13.695392Z 0 [Note] Server socket created on IP: '::'.
    2020-06-28T17:02:13.695906Z 0 [Warning] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
    2020-06-28T17:02:13.703856Z 0 [Note] InnoDB: Buffer pool(s) load completed at 200628 17:02:13
    2020-06-28T17:02:13.746239Z 0 [Note] Event Scheduler: Loaded 0 events
    2020-06-28T17:02:13.746461Z 0 [Note] mysqld: ready for connections.
    Version: '5.7.30'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)

Where we can see our mysql server is up and running

We can now test if our secrets were applied by running exact same exec syntax as in docker 
NEVER PROVIDE PASSWORD IN COMMAND LINE THIS IS JUST FOR DEMONSTRATION PURPOSES if you do just -p you will be prompted
for password

.. code-block:: bash

    kubectl exec -it mysql-deployment-579b8bb767-mk5jx -- mysql -u root -pMyPassword

    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | kubernetes_test    |
    | mysql              |
    | performance_schema |
    | sys                |
    +--------------------+
    5 rows in set (0.02 sec)


We can see initial db kubernetes_test was created
also lets try to log in to it with user and pass set up

.. code-block:: bash

    kubectl exec -it mysql-deployment-579b8bb767-mk5jx -- mysql -u django -pdjango kubernetes_test

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> 

Everything works as expected!!
