.. title: Kubernetes etcd - what's inside?
.. slug: kubernetes-etcd-whats-inside
.. date: 2021-04-10 12:17:23 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

What's etcd and what part does it play in kubernetes?
-----------------------------------------------------

ETCD is an open source distributed key-value store. In kubernetes it is a "single point of truth" as well as "single point of failure", it is the "definition of the cluster" as it holds it's configuration and status so it's best to have it replicated.

There are of course alternatives like `consul <https://www.consul.io/>`_ , `zookeeper <https://zookeeper.apache.org/>`_ , `doozerd <https://github.com/ha/doozerd/>`_ , but I can't tell anything about them yet as haven't tried them (there are a lot of comparisons on the web already).

I was always curious how it's structured, also it's good to know how it works from admin point of view this could help to simulate some etcd failure/corruption/snapshot and restore.

To play with your etcd (remember you can destroy your cluster so just bear in mind to make read operations only and dont
play with production one unless you know what you are doing).

We can do it using etcdclient locally (described in the bottom ) or eve easier - through etcd pod.

Accessing etcd through etcd pod
-------------------------------

.. code-block:: bash

    kubectl get pods -n kube-system -l component=etcd

    NAME                READY   STATUS    RESTARTS   AGE
    etcd-lenovo-node1   1/1     Running   0          47d

I now know my etcd pods name is "etcd-lenovo-node1" so I can execute etcdctl on it now.

Checking etcd instances

.. code-block:: bash

    kubectl exec -it etcd-lenovo-node1 -n kube-system -- /bin/sh -c "ETCDCTL_API=3 etcdctl \
    --endpoints=https://127.0.0.1:2379 \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --key=/etc/kubernetes/pki/etcd/server.key \
    member list"

For the reference we will get all keys:

.. code-block:: bash

    kubectl exec -it etcd-lenovo-node1 -n kube-system -- /bin/sh -c "ETCDCTL_API=3 etcdctl \
    --endpoints=https://127.0.0.1:2379 \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --key=/etc/kubernetes/pki/etcd/server.key \
    get / --prefix --keys-only"


Output:

.. code-block:: yaml

    /calico/ipam/v2/assignment/ipv4/block/10.1.129.0-26
    /calico/ipam/v2/assignment/ipv4/block/10.1.161.128-26
    /calico/ipam/v2/assignment/ipv4/block/10.1.167.64-26
    /calico/ipam/v2/assignment/ipv4/block/10.1.46.192-26
    /calico/ipam/v2/handle/ipip-tunnel-addr-lenovo-master
    /calico/ipam/v2/handle/ipip-tunnel-addr-lenovo-node1
    /calico/ipam/v2/handle/ipip-tunnel-addr-lenovo-node2
    /calico/ipam/v2/handle/ipip-tunnel-addr-lenovo-node3
    /calico/ipam/v2/handle/k8s-pod-network.066670f1f4cbac2f254078fa523c2351ff43d43f37ef279d077ca9e537363367
    /calico/ipam/v2/handle/k8s-pod-network.0854af62bdae13df75426ab9f0930045e527203c6a70863a1e6ac419dde92755
    /calico/ipam/v2/handle/k8s-pod-network.2ea6c7186cce92fcf37c68cba1013975f8144a36c24580133cdae1d2a5c81824
    /calico/ipam/v2/handle/k8s-pod-network.7b253e73a58302b7bd365f748085eddb7a88b73770f09981e4edcf743fca103e
    /calico/ipam/v2/handle/k8s-pod-network.7d44b409803e0297e38af4571fda00f7f50858fcd6b51556f94ac08561f41415
    /calico/ipam/v2/handle/k8s-pod-network.81ee68186cf6390521b6f7211804959de7dd61526b0fa50a62be68bdcdff3348
    /calico/ipam/v2/handle/k8s-pod-network.81f75b846e0b01c2756c6de53cb5ede58dcd2f08cfc0fb82b44dbbc41cb3cd83
    /calico/ipam/v2/handle/k8s-pod-network.84122c67c08645bcdc8e05024086caa16ec841018b01ed7f15bd29d837653d7f
    /calico/ipam/v2/handle/k8s-pod-network.c5942bdcf48ee4971952d449db73e4130aa7f57719d48159611ba1591f2aa5e8
    /calico/ipam/v2/handle/k8s-pod-network.de3addbfdb7c67b750b074a04d82753a3184d963255c349ed15a63597a6e7dd6
    /calico/ipam/v2/host/lenovo-master/ipv4/block/10.1.46.192-26
    /calico/ipam/v2/host/lenovo-node1/ipv4/block/10.1.161.128-26
    /calico/ipam/v2/host/lenovo-node2/ipv4/block/10.1.167.64-26
    /calico/ipam/v2/host/lenovo-node3/ipv4/block/10.1.129.0-26
    /calico/resources/v3/projectcalico.org/clusterinformations/default
    /calico/resources/v3/projectcalico.org/felixconfigurations/default
    /calico/resources/v3/projectcalico.org/felixconfigurations/node.lenovo-master
    /calico/resources/v3/projectcalico.org/felixconfigurations/node.lenovo-node1
    /calico/resources/v3/projectcalico.org/felixconfigurations/node.lenovo-node2
    /calico/resources/v3/projectcalico.org/felixconfigurations/node.lenovo-node3
    /calico/resources/v3/projectcalico.org/ippools/default-ipv4-ippool
    /calico/resources/v3/projectcalico.org/kubecontrollersconfigurations/default
    /calico/resources/v3/projectcalico.org/nodes/lenovo-master
    /calico/resources/v3/projectcalico.org/nodes/lenovo-node1
    /calico/resources/v3/projectcalico.org/nodes/lenovo-node2
    /calico/resources/v3/projectcalico.org/nodes/lenovo-node3
    /calico/resources/v3/projectcalico.org/profiles/kns.default
    /calico/resources/v3/projectcalico.org/profiles/kns.kube-node-lease
    /calico/resources/v3/projectcalico.org/profiles/kns.kube-public
    /calico/resources/v3/projectcalico.org/profiles/kns.kube-system
    /calico/resources/v3/projectcalico.org/profiles/kns.metallb-system
    /calico/resources/v3/projectcalico.org/profiles/kns.quota-mem-cpu
    /calico/resources/v3/projectcalico.org/profiles/ksa.default.default
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-node-lease.default
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-public.default
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.attachdetach-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.bootstrap-signer
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.calico-kube-controllers
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.calico-node
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.certificate-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.clusterrole-aggregation-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.coredns
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.cronjob-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.daemon-set-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.default
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.deployment-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.disruption-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.endpoint-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.endpointslice-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.endpointslicemirroring-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.expand-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.generic-garbage-collector
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.horizontal-pod-autoscaler
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.job-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.kube-proxy
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.metrics-server
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.namespace-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.node-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.persistent-volume-binder
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.pod-garbage-collector
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.pv-protection-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.pvc-protection-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.replicaset-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.replication-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.resourcequota-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.root-ca-cert-publisher
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.service-account-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.service-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.statefulset-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.token-cleaner
    /calico/resources/v3/projectcalico.org/profiles/ksa.kube-system.ttl-controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.metallb-system.controller
    /calico/resources/v3/projectcalico.org/profiles/ksa.metallb-system.default
    /calico/resources/v3/projectcalico.org/profiles/ksa.metallb-system.speaker
    /calico/resources/v3/projectcalico.org/workloadendpoints/default/lenovo--master-k8s-nginx--hpa--6c4758554f--99h7l-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/default/lenovo--master-k8s-nginx--hpa--6c4758554f--tqrp9-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/default/lenovo--master-k8s-nginx--hpa--6c4758554f--zf4rd-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/default/lenovo--node2-k8s-ng-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/default/lenovo--node2-k8s-nginx--b4c9f744d--6fqjs-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/default/lenovo--node2-k8s-nginx--b4c9f744d--hvdsh-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/kube-system/lenovo--master-k8s-metrics--server--666b5bc478--8624s-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/kube-system/lenovo--node1-k8s-coredns--74ff55c5b--n942q-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/kube-system/lenovo--node1-k8s-coredns--74ff55c5b--vnm7t-eth0
    /calico/resources/v3/projectcalico.org/workloadendpoints/metallb-system/lenovo--node3-k8s-controller--65db86ddc6--q6zvx-eth0
    /registry/apiregistration.k8s.io/apiservices/v1.
    /registry/apiregistration.k8s.io/apiservices/v1.admissionregistration.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.apiextensions.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.apps
    /registry/apiregistration.k8s.io/apiservices/v1.authentication.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.authorization.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.autoscaling
    /registry/apiregistration.k8s.io/apiservices/v1.batch
    /registry/apiregistration.k8s.io/apiservices/v1.certificates.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.coordination.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.events.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.networking.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.node.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.rbac.authorization.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.scheduling.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1.storage.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.admissionregistration.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.apiextensions.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.authentication.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.authorization.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.batch
    /registry/apiregistration.k8s.io/apiservices/v1beta1.certificates.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.coordination.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.discovery.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.events.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.extensions
    /registry/apiregistration.k8s.io/apiservices/v1beta1.flowcontrol.apiserver.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.metrics.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.networking.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.node.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.policy
    /registry/apiregistration.k8s.io/apiservices/v1beta1.rbac.authorization.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.scheduling.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v1beta1.storage.k8s.io
    /registry/apiregistration.k8s.io/apiservices/v2beta1.autoscaling
    /registry/apiregistration.k8s.io/apiservices/v2beta2.autoscaling
    /registry/clusterrolebindings/calico-kube-controllers
    /registry/clusterrolebindings/calico-node
    /registry/clusterrolebindings/cluster-admin
    /registry/clusterrolebindings/kubeadm:get-nodes
    /registry/clusterrolebindings/kubeadm:kubelet-bootstrap
    /registry/clusterrolebindings/kubeadm:node-autoapprove-bootstrap
    /registry/clusterrolebindings/kubeadm:node-autoapprove-certificate-rotation
    /registry/clusterrolebindings/kubeadm:node-proxier
    /registry/clusterrolebindings/metallb-system:controller
    /registry/clusterrolebindings/metallb-system:speaker
    /registry/clusterrolebindings/metrics-server:system:auth-delegator
    /registry/clusterrolebindings/system:basic-user
    /registry/clusterrolebindings/system:controller:attachdetach-controller
    /registry/clusterrolebindings/system:controller:certificate-controller
    /registry/clusterrolebindings/system:controller:clusterrole-aggregation-controller
    /registry/clusterrolebindings/system:controller:cronjob-controller
    /registry/clusterrolebindings/system:controller:daemon-set-controller
    /registry/clusterrolebindings/system:controller:deployment-controller
    /registry/clusterrolebindings/system:controller:disruption-controller
    /registry/clusterrolebindings/system:controller:endpoint-controller
    /registry/clusterrolebindings/system:controller:endpointslice-controller
    /registry/clusterrolebindings/system:controller:endpointslicemirroring-controller
    /registry/clusterrolebindings/system:controller:expand-controller
    /registry/clusterrolebindings/system:controller:generic-garbage-collector
    /registry/clusterrolebindings/system:controller:horizontal-pod-autoscaler
    /registry/clusterrolebindings/system:controller:job-controller
    /registry/clusterrolebindings/system:controller:namespace-controller
    /registry/clusterrolebindings/system:controller:node-controller
    /registry/clusterrolebindings/system:controller:persistent-volume-binder
    /registry/clusterrolebindings/system:controller:pod-garbage-collector
    /registry/clusterrolebindings/system:controller:pv-protection-controller
    /registry/clusterrolebindings/system:controller:pvc-protection-controller
    /registry/clusterrolebindings/system:controller:replicaset-controller
    /registry/clusterrolebindings/system:controller:replication-controller
    /registry/clusterrolebindings/system:controller:resourcequota-controller
    /registry/clusterrolebindings/system:controller:root-ca-cert-publisher
    /registry/clusterrolebindings/system:controller:route-controller
    /registry/clusterrolebindings/system:controller:service-account-controller
    /registry/clusterrolebindings/system:controller:service-controller
    /registry/clusterrolebindings/system:controller:statefulset-controller
    /registry/clusterrolebindings/system:controller:ttl-controller
    /registry/clusterrolebindings/system:coredns
    /registry/clusterrolebindings/system:discovery
    /registry/clusterrolebindings/system:kube-controller-manager
    /registry/clusterrolebindings/system:kube-dns
    /registry/clusterrolebindings/system:kube-scheduler
    /registry/clusterrolebindings/system:metrics-server
    /registry/clusterrolebindings/system:monitoring
    /registry/clusterrolebindings/system:node
    /registry/clusterrolebindings/system:node-proxier
    /registry/clusterrolebindings/system:public-info-viewer
    /registry/clusterrolebindings/system:service-account-issuer-discovery
    /registry/clusterrolebindings/system:volume-scheduler
    /registry/clusterroles/admin
    /registry/clusterroles/calico-kube-controllers
    /registry/clusterroles/calico-node
    /registry/clusterroles/cluster-admin
    /registry/clusterroles/edit
    /registry/clusterroles/kubeadm:get-nodes
    /registry/clusterroles/metallb-system:controller
    /registry/clusterroles/metallb-system:speaker
    /registry/clusterroles/system:aggregate-to-admin
    /registry/clusterroles/system:aggregate-to-edit
    /registry/clusterroles/system:aggregate-to-view
    /registry/clusterroles/system:aggregated-metrics-reader
    /registry/clusterroles/system:auth-delegator
    /registry/clusterroles/system:basic-user
    /registry/clusterroles/system:certificates.k8s.io:certificatesigningrequests:nodeclient
    /registry/clusterroles/system:certificates.k8s.io:certificatesigningrequests:selfnodeclient
    /registry/clusterroles/system:certificates.k8s.io:kube-apiserver-client-approver
    /registry/clusterroles/system:certificates.k8s.io:kube-apiserver-client-kubelet-approver
    /registry/clusterroles/system:certificates.k8s.io:kubelet-serving-approver
    /registry/clusterroles/system:certificates.k8s.io:legacy-unknown-approver
    /registry/clusterroles/system:controller:attachdetach-controller
    /registry/clusterroles/system:controller:certificate-controller
    /registry/clusterroles/system:controller:clusterrole-aggregation-controller
    /registry/clusterroles/system:controller:cronjob-controller
    /registry/clusterroles/system:controller:daemon-set-controller
    /registry/clusterroles/system:controller:deployment-controller
    /registry/clusterroles/system:controller:disruption-controller
    /registry/clusterroles/system:controller:endpoint-controller
    /registry/clusterroles/system:controller:endpointslice-controller
    /registry/clusterroles/system:controller:endpointslicemirroring-controller
    /registry/clusterroles/system:controller:expand-controller
    /registry/clusterroles/system:controller:generic-garbage-collector
    /registry/clusterroles/system:controller:horizontal-pod-autoscaler
    /registry/clusterroles/system:controller:job-controller
    /registry/clusterroles/system:controller:namespace-controller
    /registry/clusterroles/system:controller:node-controller
    /registry/clusterroles/system:controller:persistent-volume-binder
    /registry/clusterroles/system:controller:pod-garbage-collector
    /registry/clusterroles/system:controller:pv-protection-controller
    /registry/clusterroles/system:controller:pvc-protection-controller
    /registry/clusterroles/system:controller:replicaset-controller
    /registry/clusterroles/system:controller:replication-controller
    /registry/clusterroles/system:controller:resourcequota-controller
    /registry/clusterroles/system:controller:root-ca-cert-publisher
    /registry/clusterroles/system:controller:route-controller
    /registry/clusterroles/system:controller:service-account-controller
    /registry/clusterroles/system:controller:service-controller
    /registry/clusterroles/system:controller:statefulset-controller
    /registry/clusterroles/system:controller:ttl-controller
    /registry/clusterroles/system:coredns
    /registry/clusterroles/system:discovery
    /registry/clusterroles/system:heapster
    /registry/clusterroles/system:kube-aggregator
    /registry/clusterroles/system:kube-controller-manager
    /registry/clusterroles/system:kube-dns
    /registry/clusterroles/system:kube-scheduler
    /registry/clusterroles/system:kubelet-api-admin
    /registry/clusterroles/system:metrics-server
    /registry/clusterroles/system:monitoring
    /registry/clusterroles/system:node
    /registry/clusterroles/system:node-bootstrapper
    /registry/clusterroles/system:node-problem-detector
    /registry/clusterroles/system:node-proxier
    /registry/clusterroles/system:persistent-volume-provisioner
    /registry/clusterroles/system:public-info-viewer
    /registry/clusterroles/system:service-account-issuer-discovery
    /registry/clusterroles/system:volume-scheduler
    /registry/clusterroles/view
    /registry/configmaps/default/cfindex
    /registry/configmaps/default/kube-root-ca.crt
    /registry/configmaps/kube-node-lease/kube-root-ca.crt
    /registry/configmaps/kube-public/cluster-info
    /registry/configmaps/kube-public/kube-root-ca.crt
    /registry/configmaps/kube-system/calico-config
    /registry/configmaps/kube-system/coredns
    /registry/configmaps/kube-system/extension-apiserver-authentication
    /registry/configmaps/kube-system/kube-proxy
    /registry/configmaps/kube-system/kube-root-ca.crt
    /registry/configmaps/kube-system/kubeadm-config
    /registry/configmaps/kube-system/kubelet-config-1.20
    /registry/configmaps/metallb-system/kube-root-ca.crt
    /registry/controllerrevisions/kube-system/calico-node-849b8dc6bf
    /registry/controllerrevisions/kube-system/kube-proxy-9978ddf98
    /registry/controllerrevisions/metallb-system/speaker-55466f8f44
    /registry/csinodes/lenovo-master
    /registry/csinodes/lenovo-node1
    /registry/csinodes/lenovo-node2
    /registry/csinodes/lenovo-node3
    /registry/daemonsets/kube-system/calico-node
    /registry/daemonsets/kube-system/kube-proxy
    /registry/daemonsets/metallb-system/speaker
    /registry/deployments/default/nginx
    /registry/deployments/kube-system/calico-kube-controllers
    /registry/deployments/kube-system/coredns
    /registry/deployments/kube-system/metrics-server
    /registry/deployments/metallb-system/controller
    /registry/endpointslices/default/kubernetes
    /registry/endpointslices/default/nginx-gr59r
    /registry/endpointslices/default/ngnix-service-sffvv
    /registry/endpointslices/default/test1-fv4vr
    /registry/endpointslices/kube-system/kube-dns-hjqkv
    /registry/endpointslices/kube-system/kubelet-2fkdq
    /registry/endpointslices/kube-system/metrics-server-j47kl
    /registry/flowschemas/catch-all
    /registry/flowschemas/exempt
    /registry/flowschemas/global-default
    /registry/flowschemas/kube-controller-manager
    /registry/flowschemas/kube-scheduler
    /registry/flowschemas/kube-system-service-accounts
    /registry/flowschemas/service-accounts
    /registry/flowschemas/system-leader-election
    /registry/flowschemas/system-nodes
    /registry/flowschemas/workload-leader-election
    /registry/leases/kube-node-lease/lenovo-master
    /registry/leases/kube-node-lease/lenovo-node1
    /registry/leases/kube-node-lease/lenovo-node2
    /registry/leases/kube-node-lease/lenovo-node3
    /registry/leases/kube-system/kube-controller-manager
    /registry/leases/kube-system/kube-scheduler
    /registry/masterleases/192.168.1.131
    /registry/minions/lenovo-master
    /registry/minions/lenovo-node1
    /registry/minions/lenovo-node2
    /registry/minions/lenovo-node3
    /registry/namespaces/default
    /registry/namespaces/kube-node-lease
    /registry/namespaces/kube-public
    /registry/namespaces/kube-system
    /registry/namespaces/metallb-system
    /registry/namespaces/quota-mem-cpu
    /registry/poddisruptionbudgets/kube-system/calico-kube-controllers
    /registry/pods/default/ng
    /registry/pods/default/nginx-b4c9f744d-6fqjs
    /registry/pods/default/nginx-b4c9f744d-hvdsh
    /registry/pods/default/nginx-hpa-6c4758554f-99h7l
    /registry/pods/default/nginx-hpa-6c4758554f-tqrp9
    /registry/pods/default/nginx-hpa-6c4758554f-zf4rd
    /registry/pods/kube-system/calico-kube-controllers-664b5654ff-lmfjw
    /registry/pods/kube-system/calico-node-6vtln
    /registry/pods/kube-system/calico-node-9psrj
    /registry/pods/kube-system/calico-node-n64kf
    /registry/pods/kube-system/calico-node-s4gnp
    /registry/pods/kube-system/coredns-74ff55c5b-n942q
    /registry/pods/kube-system/coredns-74ff55c5b-vnm7t
    /registry/pods/kube-system/etcd-lenovo-node1
    /registry/pods/kube-system/kube-apiserver-lenovo-node1
    /registry/pods/kube-system/kube-controller-manager-lenovo-node1
    /registry/pods/kube-system/kube-proxy-dxtr2
    /registry/pods/kube-system/kube-proxy-r7jpl
    /registry/pods/kube-system/kube-proxy-sb4b6
    /registry/pods/kube-system/kube-proxy-v9xck
    /registry/pods/kube-system/kube-scheduler-lenovo-node1
    /registry/pods/kube-system/metrics-server-666b5bc478-8624s
    /registry/pods/metallb-system/controller-65db86ddc6-q6zvx
    /registry/pods/metallb-system/speaker-6mzwx
    /registry/pods/metallb-system/speaker-btrtz
    /registry/pods/metallb-system/speaker-pxf28
    /registry/podsecuritypolicy/controller
    /registry/podsecuritypolicy/speaker
    /registry/priorityclasses/system-cluster-critical
    /registry/priorityclasses/system-node-critical
    /registry/prioritylevelconfigurations/catch-all
    /registry/prioritylevelconfigurations/exempt
    /registry/prioritylevelconfigurations/global-default
    /registry/prioritylevelconfigurations/leader-election
    /registry/prioritylevelconfigurations/system
    /registry/prioritylevelconfigurations/workload-high
    /registry/prioritylevelconfigurations/workload-low
    /registry/ranges/serviceips
    /registry/ranges/servicenodeports
    /registry/replicasets/default/nginx-6799fc88d8
    /registry/replicasets/default/nginx-6c54d6848f
    /registry/replicasets/default/nginx-b4c9f744d
    /registry/replicasets/kube-system/calico-kube-controllers-664b5654ff
    /registry/replicasets/kube-system/coredns-74ff55c5b
    /registry/replicasets/kube-system/metrics-server-666b5bc478
    /registry/replicasets/metallb-system/controller-65db86ddc6
    /registry/rolebindings/kube-public/kubeadm:bootstrap-signer-clusterinfo
    /registry/rolebindings/kube-public/system:controller:bootstrap-signer
    /registry/rolebindings/kube-system/kube-proxy
    /registry/rolebindings/kube-system/kubeadm:kubelet-config-1.20
    /registry/rolebindings/kube-system/kubeadm:nodes-kubeadm-config
    /registry/rolebindings/kube-system/metrics-server-auth-reader
    /registry/rolebindings/kube-system/system::extension-apiserver-authentication-reader
    /registry/rolebindings/kube-system/system::leader-locking-kube-controller-manager
    /registry/rolebindings/kube-system/system::leader-locking-kube-scheduler
    /registry/rolebindings/kube-system/system:controller:bootstrap-signer
    /registry/rolebindings/kube-system/system:controller:cloud-provider
    /registry/rolebindings/kube-system/system:controller:token-cleaner
    /registry/rolebindings/metallb-system/config-watcher
    /registry/rolebindings/metallb-system/pod-lister
    /registry/roles/kube-public/kubeadm:bootstrap-signer-clusterinfo
    /registry/roles/kube-public/system:controller:bootstrap-signer
    /registry/roles/kube-system/extension-apiserver-authentication-reader
    /registry/roles/kube-system/kube-proxy
    /registry/roles/kube-system/kubeadm:kubelet-config-1.20
    /registry/roles/kube-system/kubeadm:nodes-kubeadm-config
    /registry/roles/kube-system/system::leader-locking-kube-controller-manager
    /registry/roles/kube-system/system::leader-locking-kube-scheduler
    /registry/roles/kube-system/system:controller:bootstrap-signer
    /registry/roles/kube-system/system:controller:cloud-provider
    /registry/roles/kube-system/system:controller:token-cleaner
    /registry/roles/metallb-system/config-watcher
    /registry/roles/metallb-system/pod-lister
    /registry/secrets/default/default-token-qknwm
    /registry/secrets/kube-node-lease/default-token-xhxwz
    /registry/secrets/kube-public/default-token-767ld
    /registry/secrets/kube-system/attachdetach-controller-token-rm5kc
    /registry/secrets/kube-system/bootstrap-signer-token-fwnzd
    /registry/secrets/kube-system/calico-etcd-secrets
    /registry/secrets/kube-system/calico-kube-controllers-token-h4trc
    /registry/secrets/kube-system/calico-node-token-js7t8
    /registry/secrets/kube-system/certificate-controller-token-pk96t
    /registry/secrets/kube-system/clusterrole-aggregation-controller-token-xxb5s
    /registry/secrets/kube-system/coredns-token-b2z2f
    /registry/secrets/kube-system/cronjob-controller-token-54p6d
    /registry/secrets/kube-system/daemon-set-controller-token-sbtsk
    /registry/secrets/kube-system/default-token-9fhbc
    /registry/secrets/kube-system/deployment-controller-token-swxcw
    /registry/secrets/kube-system/disruption-controller-token-2rr6w
    /registry/secrets/kube-system/endpoint-controller-token-fmjrz
    /registry/secrets/kube-system/endpointslice-controller-token-sbn6n
    /registry/secrets/kube-system/endpointslicemirroring-controller-token-qrld7
    /registry/secrets/kube-system/expand-controller-token-tfgpk
    /registry/secrets/kube-system/generic-garbage-collector-token-nc855
    /registry/secrets/kube-system/horizontal-pod-autoscaler-token-h8rl9
    /registry/secrets/kube-system/job-controller-token-d7lnj
    /registry/secrets/kube-system/kube-proxy-token-9snst
    /registry/secrets/kube-system/metrics-server-token-szltz
    /registry/secrets/kube-system/namespace-controller-token-rwn7m
    /registry/secrets/kube-system/node-controller-token-zqvxv
    /registry/secrets/kube-system/persistent-volume-binder-token-6vj8p
    /registry/secrets/kube-system/pod-garbage-collector-token-77gp8
    /registry/secrets/kube-system/pv-protection-controller-token-49c2m
    /registry/secrets/kube-system/pvc-protection-controller-token-twhrk
    /registry/secrets/kube-system/replicaset-controller-token-d4bzb
    /registry/secrets/kube-system/replication-controller-token-7mprg
    /registry/secrets/kube-system/resourcequota-controller-token-x97qt
    /registry/secrets/kube-system/root-ca-cert-publisher-token-gr4cq
    /registry/secrets/kube-system/service-account-controller-token-46wxl
    /registry/secrets/kube-system/service-controller-token-dbnc5
    /registry/secrets/kube-system/statefulset-controller-token-fxblr
    /registry/secrets/kube-system/token-cleaner-token-c48kq
    /registry/secrets/kube-system/ttl-controller-token-q5wmc
    /registry/secrets/metallb-system/controller-token-9vrqd
    /registry/secrets/metallb-system/default-token-9jw8j
    /registry/secrets/metallb-system/memberlist
    /registry/secrets/metallb-system/speaker-token-d6b7b
    /registry/serviceaccounts/default/default
    /registry/serviceaccounts/kube-node-lease/default
    /registry/serviceaccounts/kube-public/default
    /registry/serviceaccounts/kube-system/attachdetach-controller
    /registry/serviceaccounts/kube-system/bootstrap-signer
    /registry/serviceaccounts/kube-system/calico-kube-controllers
    /registry/serviceaccounts/kube-system/calico-node
    /registry/serviceaccounts/kube-system/certificate-controller
    /registry/serviceaccounts/kube-system/clusterrole-aggregation-controller
    /registry/serviceaccounts/kube-system/coredns
    /registry/serviceaccounts/kube-system/cronjob-controller
    /registry/serviceaccounts/kube-system/daemon-set-controller
    /registry/serviceaccounts/kube-system/default
    /registry/serviceaccounts/kube-system/deployment-controller
    /registry/serviceaccounts/kube-system/disruption-controller
    /registry/serviceaccounts/kube-system/endpoint-controller
    /registry/serviceaccounts/kube-system/endpointslice-controller
    /registry/serviceaccounts/kube-system/endpointslicemirroring-controller
    /registry/serviceaccounts/kube-system/expand-controller
    /registry/serviceaccounts/kube-system/generic-garbage-collector
    /registry/serviceaccounts/kube-system/horizontal-pod-autoscaler
    /registry/serviceaccounts/kube-system/job-controller
    /registry/serviceaccounts/kube-system/kube-proxy
    /registry/serviceaccounts/kube-system/metrics-server
    /registry/serviceaccounts/kube-system/namespace-controller
    /registry/serviceaccounts/kube-system/node-controller
    /registry/serviceaccounts/kube-system/persistent-volume-binder
    /registry/serviceaccounts/kube-system/pod-garbage-collector
    /registry/serviceaccounts/kube-system/pv-protection-controller
    /registry/serviceaccounts/kube-system/pvc-protection-controller
    /registry/serviceaccounts/kube-system/replicaset-controller
    /registry/serviceaccounts/kube-system/replication-controller
    /registry/serviceaccounts/kube-system/resourcequota-controller
    /registry/serviceaccounts/kube-system/root-ca-cert-publisher
    /registry/serviceaccounts/kube-system/service-account-controller
    /registry/serviceaccounts/kube-system/service-controller
    /registry/serviceaccounts/kube-system/statefulset-controller
    /registry/serviceaccounts/kube-system/token-cleaner
    /registry/serviceaccounts/kube-system/ttl-controller
    /registry/serviceaccounts/metallb-system/controller
    /registry/serviceaccounts/metallb-system/default
    /registry/serviceaccounts/metallb-system/speaker
    /registry/services/endpoints/default/kubernetes
    /registry/services/endpoints/default/nginx
    /registry/services/endpoints/default/ngnix-service
    /registry/services/endpoints/default/test1
    /registry/services/endpoints/kube-system/kube-dns
    /registry/services/endpoints/kube-system/kubelet
    /registry/services/endpoints/kube-system/metrics-server
    /registry/services/specs/default/kubernetes
    /registry/services/specs/default/nginx
    /registry/services/specs/default/ngnix-service
    /registry/services/specs/default/test1
    /registry/services/specs/kube-system/kube-dns
    /registry/services/specs/kube-system/kubelet
    /registry/services/specs/kube-system/metrics-server

Now lets check all pods with all namespaces so something like  kubectl get pods --all-namespaces


.. code-block:: bash

    kubectl exec -it etcd-lenovo-node1 -n kube-system -- /bin/sh -c "ETCDCTL_API=3 etcdctl \
    --endpoints=https://127.0.0.1:2379 \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --key=/etc/kubernetes/pki/etcd/server.key \
    get /registry/pods --prefix --keys-only

Output:


.. code-block:: bash

    ➜  etcd git:(master) ✗ kubectl get pods --all-namespaces
    NAMESPACE        NAME                                       READY   STATUS        RESTARTS   AGE
    default          ng                                         1/1     Running       0          11d
    default          nginx-b4c9f744d-6fqjs                      1/1     Running       0          10d
    default          nginx-b4c9f744d-hvdsh                      1/1     Running       0          10d
    default          nginx-hpa-6c4758554f-99h7l                 1/1     Terminating   0          45d
    default          nginx-hpa-6c4758554f-tqrp9                 1/1     Terminating   0          45d
    default          nginx-hpa-6c4758554f-zf4rd                 1/1     Terminating   0          45d
    kube-system      calico-kube-controllers-664b5654ff-lmfjw   1/1     Running       0          46d
    kube-system      calico-node-6vtln                          1/1     Running       0          46d
    kube-system      calico-node-9psrj                          1/1     Running       0          46d
    kube-system      calico-node-n64kf                          1/1     Running       0          46d
    kube-system      calico-node-s4gnp                          1/1     Running       0          46d
    kube-system      coredns-74ff55c5b-n942q                    1/1     Running       0          47d
    kube-system      coredns-74ff55c5b-vnm7t                    1/1     Running       0          47d
    kube-system      etcd-lenovo-node1                          1/1     Running       0          47d
    kube-system      kube-apiserver-lenovo-node1                1/1     Running       0          47d
    kube-system      kube-controller-manager-lenovo-node1       1/1     Running       0          47d
    kube-system      kube-proxy-dxtr2                           1/1     Running       0          47d
    kube-system      kube-proxy-r7jpl                           1/1     Running       0          47d
    kube-system      kube-proxy-sb4b6                           1/1     Running       0          47d
    kube-system      kube-proxy-v9xck                           1/1     Running       0          47d
    kube-system      kube-scheduler-lenovo-node1                1/1     Running       0          47d
    kube-system      metrics-server-666b5bc478-8624s            1/1     Running       0          45d
    metallb-system   controller-65db86ddc6-q6zvx                1/1     Running       0          33d
    metallb-system   speaker-6mzwx                              1/1     Running       0          33d
    metallb-system   speaker-btrtz                              1/1     Running       0          33d
    metallb-system   speaker-pxf28                              1/1     Running       0          33d


From the reference above - I can see pods are under /registry/pods and then the next key is namespace so to get pods in
kubesystem we need to use key /registry/pods/kube-system/

We might observe something interesting watching one of the pods:

.. code-block:: bash

    kubectl exec -it etcd-lenovo-node1 -n kube-system -- /bin/sh -c "ETCDCTL_API=3 etcdctl 
    --endpoints=https://127.0.0.1:2379 \
    --cacert=/etc/kubernetes/pki/etcd/ca.crt \
    --cert=/etc/kubernetes/pki/etcd/server.crt \
    --key=/etc/kubernetes/pki/etcd/server.key \
    watch /registry/pods/default/nginx-b4c9f744d-6fqjs "

and I will just add new label in another terminal

.. code-block:: bash

    kc label pods pod nginx-b4c9f744d-6fqjs my-new-label=test

we can see straight away the change in watch command :) 


Accessing etcd from host
------------------------

If we want to access etcdctl locally (locally means - still need to be on one of the nodes) we just need to install etcd-client

on ubuntu 

.. code-block:: bash

    sudo apt-get install etcd-client

and then we should be able to get all etcd instances with

.. code-block:: bash

    sudo ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key   member list


