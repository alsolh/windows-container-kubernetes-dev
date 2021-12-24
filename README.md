# windows-container-kubernetes-dev
Guide for creating a local development environment suitable for multi k3s cluster creation and container development on windows machines

Special thanks to my friend https://github.com/hdnebat for his guidance and advices.

# Primary tools:

Setup Docker Desktop https://www.docker.com/products/docker-desktop

https://chocolatey.org/install

Install K3D

```
choco install k3d
```

Install JQ

```
choco install jq
```

Install vscode

Install GIT https://git-scm.com/, keep default settings and make vscode the default editor

Install powershell core https://github.com/PowerShell/PowerShell

# Optional but useful tools:
https://k9scli.io/

https://k8slens.dev/

https://community.chocolatey.org/packages/istioctl

Putty and MTPutty https://ttyplus.com/multi-tabbed-putty/, https://www.putty.org/

# Useful Guides:

if you want to install portainer to manage the docker engine only:
https://docs.portainer.io/v/ce-2.6/start/install/server/docker/wsl

if you want to try kubernetes ,follow this guide for the optimum cluster configuration
https://github.com/keunlee/k3d-metallb-starter-kit

use below command to create the cluster instead to lower the load on your laptop
```
k3d cluster create my-cluster \
--network my-cluster-network \
--no-lb \
--agents 1 \
--k3s-arg '--disable=servicelb@server:0'
```

if you want example app stacks on kubernetes,docker,docker compose (docker compose is not required to learn but good to know)
https://github.com/dockersamples/example-voting-app

if you want to try rancher cluster manager gui follow this guide, ignore the requirement for rancher desktop we are using k3d + docker desktop instead:
https://community.suse.com/posts/setting-up-rancher-on-your-local-machine-with-rancher-desktop

if you want to try service mesh, follow this guide, before starting install grafana/monitoring + istio from the cluster tools page in rancher
https://istio.io/latest/docs/examples/bookinfo/

# Networking workarounds for windows:

Windows host system cannot ping or access linux containers running within WSL2, so below workarounds are required, reference: https://docs.docker.com/desktop/windows/networking/#i-cannot-ping-my-containers

if you want to connect to a service in the kubernetes cluster on a temporary basis please use below command, if you have mac you dont need it, this is a workaround due to windows networking limitations for linux containers in docker desktop:
```
kubectl port-forward svc/nginx 9090:80
```
if you instead need a permanent connection (maintained even after restart), below command, if you have mac you dont need it, this is a workaround due to windows networking limitations for linux containers in docker desktop:
```
docker run \
-d \
-p <local-port>:<local-port> \
--name=<k3d-host>-<k3d-port>-link \
--network <k3d-namespace> \
alpine/socat \
TCP4-LISTEN:<local-port>,fork,reuseaddr \
TCP4:<k3d-host>:<k3d-port>
```
example:
```
docker run \
-d \
-p 9091:9091 \
--name=k3d-nginxtest-80-link \
--network my-cluster-network \
alpine/socat \
TCP4-LISTEN:9091,fork,reuseaddr \
TCP4:172.30.210.1:80
```
reference: https://github.com/rancher/k3d/issues/89

Happy Containering, Clustering, DevOpsing! :)
