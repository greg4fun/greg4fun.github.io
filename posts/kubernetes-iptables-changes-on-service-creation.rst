.. title: Kubernetes iptables changes on service creation
.. slug: kubernetes-iptables-changes-on-service-creation
.. date: 2021-04-14 21:37:51 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

This is simple investigation on what happens with iptables on service creation with /and without targetport specified

I've been recently told by someone that using target port in service creates mess in iptables so I thought it will be
cool challenge to check if thats really the case.

I have used simple iptables-save to get whole iptables rules at once and then added service with targetport, then cleaned iptables by deleting service and
then did the same without targetport to compare what is being added to ipt.

Cluster specs:

* CPU ARCH: (x86) build with kubeadm:
* CNI: Calico
* KubeProxy mode: iptables
* standard etcd

IPtables with targetport in service
-----------------------------------

.. code-block:: bash

    > :KUBE-SEP-KVGH6HHOFLBGG2WW - [0:0] 184a186
    > :KUBE-SVC-FOI3G5ZK27IESILB - [0:0] 201a204,205
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix-service" -m tcp --dport 31224 -j KUBE-MARK-MASQ
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix-service" -m tcp --dport 31224 -j KUBE-SVC-FOI3G5ZK27IESILB
    > -A KUBE-SEP-KVGH6HHOFLBGG2WW -s 10.1.167.92/32 -m comment --comment "default/ngnix-service" -j KUBE-MARK-MASQ
    > -A KUBE-SEP-KVGH6HHOFLBGG2WW -p tcp -m comment --comment "default/ngnix-service" -m tcp -j DNAT --to-destination 10.1.167.92:8000
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.105.57.223/32 -p tcp -m comment --comment "default/ngnix-service cluster IP" -m tcp --dport 80 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.105.57.223/32 -p tcp -m comment --comment "default/ngnix-service cluster IP" -m tcp --dport 80 -j KUBE-SVC-FOI3G5ZK27IESILB
    > -A KUBE-SVC-FOI3G5ZK27IESILB -m comment --comment "default/ngnix-service" -j KUBE-SEP-KVGH6HHOFLBGG2WW 

As we can see in the example above the rule is to destination nat pod IP 10.1.167.92 on the port 8000 which is target
port we have specified.

IPtables without targetport in service 
--------------------------------------

.. code-block:: bash

    > :KUBE-SEP-OP54BO3C6MKRBI5R - [0:0]
    > :KUBE-SVC-FOI3G5ZK27IESILB - [0:0]
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix-service" -m tcp --dport 32681 -j KUBE-MARK-MASQ
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix-service" -m tcp --dport 32681 -j KUBE-SVC-FOI3G5ZK27IESILB
    > -A KUBE-SEP-OP54BO3C6MKRBI5R -s 10.1.167.92/32 -m comment --comment "default/ngnix-service" -j KUBE-MARK-MASQ
    > -A KUBE-SEP-OP54BO3C6MKRBI5R -p tcp -m comment --comment "default/ngnix-service" -m tcp -j DNAT --to-destination 10.1.167.92:80
    < -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-MARK-MASQ
    < -A KUBE-SERVICES -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-SVC-NPX46M4PTMTKRN6Y
    < -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.0.10/32 -p tcp -m comment --comment "kube-system/kube-dns:metrics cluster IP" -m tcp --dport 9153 -j KUBE-MARK-MASQ
    < -A KUBE-SERVICES -d 10.96.0.10/32 -p tcp -m comment --comment "kube-system/kube-dns:metrics cluster IP" -m tcp --dport 9153 -j KUBE-SVC-JD5MR3NA4I4DYORP
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.129.116/32 -p tcp -m comment --comment "default/ngnix-service cluster IP" -m tcp --dport 80 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.96.129.116/32 -p tcp -m comment --comment "default/ngnix-service cluster IP" -m tcp --dport 80 -j KUBE-SVC-FOI3G5ZK27IESILB
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.96.0.1/32 -p tcp -m comment --comment "default/kubernetes:https cluster IP" -m tcp --dport 443 -j KUBE-SVC-NPX46M4PTMTKRN6Y
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.0.10/32 -p tcp -m comment --comment "kube-system/kube-dns:metrics cluster IP" -m tcp --dport 9153 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.96.0.10/32 -p tcp -m comment --comment "kube-system/kube-dns:metrics cluster IP" -m tcp --dport 9153 -j KUBE-SVC-JD5MR3NA4I4DYORP
    > -A KUBE-SVC-FOI3G5ZK27IESILB -m comment --comment "default/ngnix-service" -j KUBE-SEP-OP54BO3C6MKRBI5R


In such simple setup I would say not providing the targetport makes even bigger mess.



Lets see something more sophisticated so deployment with 2 replicasets

With targetport:
----------------

.. code-block:: bash

    > :PREROUTING ACCEPT [4:212]
    > :INPUT ACCEPT [4:212]
    > :OUTPUT ACCEPT [29:1740]
    > :POSTROUTING ACCEPT [29:1740]
    > :KUBE-SEP-KCPMBF3JPX5ITGQR - [0:0]
    > :KUBE-SEP-PPG4JXRVDYEFVT6U - [0:0]
    > :KUBE-SVC-JSEMNMAXFXXWPYZQ - [0:0]
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix2-service" -m tcp --dport 30329 -j KUBE-MARK-MASQ
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix2-service" -m tcp --dport 30329 -j KUBE-SVC-JSEMNMAXFXXWPYZQ
    > -A KUBE-SEP-KCPMBF3JPX5ITGQR -s 10.1.129.5/32 -m comment --comment "default/ngnix2-service" -j KUBE-MARK-MASQ
    > -A KUBE-SEP-KCPMBF3JPX5ITGQR -p tcp -m comment --comment "default/ngnix2-service" -m tcp -j DNAT --to-destination 10.1.129.5:8000
    > -A KUBE-SEP-PPG4JXRVDYEFVT6U -s 10.1.167.83/32 -m comment --comment "default/ngnix2-service" -j KUBE-MARK-MASQ
    > -A KUBE-SEP-PPG4JXRVDYEFVT6U -p tcp -m comment --comment "default/ngnix2-service" -m tcp -j DNAT --to-destination 10.1.167.83:8000
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.98.111.212/32 -p tcp -m comment --comment "default/ngnix2-service cluster IP" -m tcp --dport 80 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.98.111.212/32 -p tcp -m comment --comment "default/ngnix2-service cluster IP" -m tcp --dport 80 -j KUBE-SVC-JSEMNMAXFXXWPYZQ
    > -A KUBE-SVC-JSEMNMAXFXXWPYZQ -m comment --comment "default/ngnix2-service" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-KCPMBF3JPX5ITGQR
    > -A KUBE-SVC-JSEMNMAXFXXWPYZQ -m comment --comment "default/ngnix2-service" -j KUBE-SEP-PPG4JXRVDYEFVT6U
 
From the following we can spot that each for our label selector in the service is listed here,
which absolutely makes sense how kube proxy would know where to send packets if not that.

Ok lets try with replicas without providing the port:

Without targetport:
-------------------

.. code-block:: bash

    > :PREROUTING ACCEPT [4:252]
    > :INPUT ACCEPT [4:252]
    > :OUTPUT ACCEPT [25:1500]
    > :POSTROUTING ACCEPT [25:1500]
    > :KUBE-SEP-NK6MJN7AMVFQPBDQ - [0:0]
    > :KUBE-SEP-ZX65TQ3QUDHUAQQM - [0:0]
    > :KUBE-SVC-JSEMNMAXFXXWPYZQ - [0:0]
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix2-service" -m tcp --dport 31277 -j KUBE-MARK-MASQ
    > -A KUBE-NODEPORTS -p tcp -m comment --comment "default/ngnix2-service" -m tcp --dport 31277 -j KUBE-SVC-JSEMNMAXFXXWPYZQ
    > -A KUBE-SEP-NK6MJN7AMVFQPBDQ -s 10.1.129.5/32 -m comment --comment "default/ngnix2-service" -j KUBE-MARK-MASQ
    > -A KUBE-SEP-NK6MJN7AMVFQPBDQ -p tcp -m comment --comment "default/ngnix2-service" -m tcp -j DNAT --to-destination 10.1.129.5:80
    < -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.0.10/32 -p udp -m comment --comment "kube-system/kube-dns:dns cluster IP" -m udp --dport 53 -j KUBE-MARK-MASQ
    < -A KUBE-SERVICES -d 10.96.0.10/32 -p udp -m comment --comment "kube-system/kube-dns:dns cluster IP" -m udp --dport 53 -j KUBE-SVC-TCOU7JCQXEZGVUNU
    ---
    > -A KUBE-SEP-ZX65TQ3QUDHUAQQM -s 10.1.167.83/32 -m comment --comment "default/ngnix2-service" -j KUBE-MARK-MASQ
    > -A KUBE-SEP-ZX65TQ3QUDHUAQQM -p tcp -m comment --comment "default/ngnix2-service" -m tcp -j DNAT --to-destination 10.1.167.83:80
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.108.13.83/32 -p tcp -m comment --comment "default/ngnix2-service cluster IP" -m tcp --dport 80 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.108.13.83/32 -p tcp -m comment --comment "default/ngnix2-service cluster IP" -m tcp --dport 80 -j KUBE-SVC-JSEMNMAXFXXWPYZQ
    > -A KUBE-SERVICES ! -s 192.168.0.0/16 -d 10.96.0.10/32 -p udp -m comment --comment "kube-system/kube-dns:dns cluster IP" -m udp --dport 53 -j KUBE-MARK-MASQ
    > -A KUBE-SERVICES -d 10.96.0.10/32 -p udp -m comment --comment "kube-system/kube-dns:dns cluster IP" -m udp --dport 53 -j KUBE-SVC-TCOU7JCQXEZGVUNU
    > -A KUBE-SVC-JSEMNMAXFXXWPYZQ -m comment --comment "default/ngnix2-service" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-NK6MJN7AMVFQPBDQ
    > -A KUBE-SVC-JSEMNMAXFXXWPYZQ -m comment --comment "default/ngnix2-service" -j KUBE-SEP-ZX65TQ3QUDHUAQQM

This needs proper investigation from me but for now what I can see 
by not providing target port the iptables rules are interfering with more components like kube-dns
and  by providing the targetport its not touching kube-dns

To be continued...
