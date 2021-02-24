.. title: Kubernetes load testing with autoscaling
.. slug: kubernetes-load-testing-with-autoscaling
.. date: 2021-02-23 19:26:15 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Having my cluster ready I wanted to load test it and learn some autoscaling k8s features.

To see our pods usage we need to install metrics server for test purposes I will modify the yaml and add there --kubelet-insecure-tls option


wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
vim components.yaml and search for container args and add --kubelet-insecure-ts  optio there REMEMBER its just for
homelab - never do it in prod.

