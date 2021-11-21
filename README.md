- install tools (via homebrew, assuming on macOS)
```
➜  ~ brew install kubectl doctl
➜  ~ curl -s -L "https://github.com/loft-sh/vcluster/releases/latest" | sed -nE 's!.*"([^"]*vcluster-darwin-amd64)".*!https://github.com\1!p' | xargs -n 1 curl -L -o vcluster && chmod +x vcluster;
➜  ~ sudo mv vcluster /usr/local/bin;
```

- start kubernetes cluster in DO
```
➜  ~ doctl auth init
Please authenticate doctl for use with your DigitalOcean account. You can generate a token in the control panel at https://cloud.digitalocean.com/account/api/tokens

Enter your access token:
Validating token... OK
➜  ~ doctl kubernetes cluster create do-kubernetes-challenge --region sgp1
Notice: Cluster is provisioning, waiting for cluster to be running
.........................................................................
Notice: Cluster created, fetching credentials
Notice: Adding cluster credentials to kubeconfig file found in "/Users/dick/.kube/config"
Notice: Setting current-context to do-sgp1-do-kubernetes-challenge
ID                                      Name                       Region    Version        Auto Upgrade    Status     Node Pools
3a8c9561-170f-4efc-b796-0f10167b013c    do-kubernetes-challenge    sgp1      1.21.5-do.0    true            running    do-kubernetes-challenge-default-pool
➜  ~ kubectl get nodes
NAME                                         STATUS   ROLES    AGE     VERSION
do-kubernetes-challenge-default-pool-ujrff   Ready    <none>   5m42s   v1.21.5
do-kubernetes-challenge-default-pool-ujrfm   Ready    <none>   5m38s   v1.21.5
do-kubernetes-challenge-default-pool-ujrfq   Ready    <none>   5m44s   v1.21.5
➜  ~ kubectl get pods --all-namespaces
NAMESPACE          NAME                                                  READY   STATUS    RESTARTS   AGE
host-namespace-1   coredns-7448499f4d-ggs6w-x-kube-system-x-vcluster-1   1/1     Running   0          4m11s
host-namespace-1   vcluster-1-0                                          2/2     Running   0          5m12s
kube-system        cilium-4cb2g                                          1/1     Running   0          11m
kube-system        cilium-dlnfs                                          1/1     Running   0          11m
kube-system        cilium-operator-79c49d8fd6-8kxhj                      1/1     Running   0          14m
kube-system        cilium-qxqc4                                          1/1     Running   0          11m
kube-system        coredns-85d9ccbb46-2c8lj                              1/1     Running   0          14m
kube-system        coredns-85d9ccbb46-lbjrk                              1/1     Running   0          14m
kube-system        csi-do-node-gkdgs                                     2/2     Running   0          11m
kube-system        csi-do-node-mc2cd                                     2/2     Running   0          11m
kube-system        csi-do-node-sfsfs                                     2/2     Running   0          11m
kube-system        do-node-agent-6fcjh                                   1/1     Running   0          11m
kube-system        do-node-agent-7bxzm                                   1/1     Running   0          11m
kube-system        do-node-agent-hmzfj                                   1/1     Running   0          11m
kube-system        kube-proxy-ptldc                                      1/1     Running   0          11m
kube-system        kube-proxy-sqtmb                                      1/1     Running   0          11m
kube-system        kube-proxy-v7gkt                                      1/1     Running   0          11m
```

- create vcluster
```
➜  ~ vcluster create vcluster-1 -n host-namespace-1
[info]   Creating namespace host-namespace-1
[info]   execute command: helm upgrade vcluster-1 vcluster --repo https://charts.loft.sh --version 0.4.4 --kubeconfig /var/folders/5y/m0mtm18x78l_yqr0jb4tsl7h0000gq/T/624956454 --namespace host-namespace-1 --install --repository-config='' --values /var/folders/5y/m0mtm18x78l_yqr0jb4tsl7h0000gq/T/231038797
[done] √ Successfully created virtual cluster vcluster-1 in namespace host-namespace-1. Use 'vcluster connect vcluster-1 --namespace host-namespace-1' to access the virtual cluster
➜  ~ vcluster connect vcluster-1 --namespace host-namespace-1
[done] √ Virtual cluster kube config written to: ./kubeconfig.yaml. You can access the cluster via `kubectl --kubeconfig ./kubeconfig.yaml get namespaces`
[info]   Starting port-forwarding at 8443:8443
Forwarding from 127.0.0.1:8443 -> 8443
Forwarding from [::1]:8443 -> 8443
Handling connection for 8443
Handling connection for 8443
Handling connection for 8443
Handling connection for 8443
```

- connect to vcluster (in another terminal)
```
➜  ~ export KUBECONFIG=$PWD/kubeconfig.yaml
➜  ~ kubectl get nodes
NAME                                         STATUS   ROLES    AGE   VERSION
do-kubernetes-challenge-default-pool-ujrff   Ready    <none>   92s   v1.21.5+k3s2
➜  ~ kubectl get pod --all-namespaces
NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
kube-system   coredns-7448499f4d-ggs6w   1/1     Running   0          111s
```

- awesome done!
