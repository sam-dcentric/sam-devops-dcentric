
# Project Title

DevOpsSec Takehome Test

Candidate name: Samuel Hutapea

## [DevOps Skill] Kubernetes Deployment Use Case

### Requirement
#### Install minikube using brew

```http
  brew install minikube
```

#### Install kubectl using brew

```http
  brew install kubectl
```

#### Install helm using brew

```http
  brew install helm
```
---


### Minikube start

```http
  minikube start --memory 1800 --cpus 2 -p dcentric-minikube --nodes 3
```
Reference : https://minikube.sigs.k8s.io/docs/start/

Check total of node after starting minikube



```shell
sam-devops-dcentric git:(main) ✗ kubectl get nodes
NAME                    STATUS   ROLES           AGE    VERSION
dcentric-minikube       Ready    control-plane   176m   v1.26.3
dcentric-minikube-m02   Ready    <none>          107m   v1.26.3
dcentric-minikube-m03   Ready    <none>          107m   v1.26.3
```

### Labeling Node

Add label on control-panel node
```shell
kubectl label nodes dcentric-minikube Node=A
```

Add label on worker node dcentric-minikube-m02
```shell
kubectl label nodes dcentric-minikube-m02 Node=B
```

Add label on worker node dcentric-minikube-m03
```shell
kubectl label nodes dcentric-minikube-m03 Node=C
```

Check label on each node
```shell
sam-devops-dcentric git:(main) ✗ kubectl get nodes --show-labels
NAME                    STATUS   ROLES           AGE    VERSION   LABELS
dcentric-minikube       Ready    control-plane   3h7m   v1.26.3   Node=A,beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=dcentric-minikube,kubernetes.io/os=linux,minikube.k8s.io/commit=08896fd1dc362c097c925146c4a0d0dac715ace0,minikube.k8s.io/name=dcentric-minikube,minikube.k8s.io/primary=true,minikube.k8s.io/updated_at=2023_06_11T21_55_32_0700,minikube.k8s.io/version=v1.30.1,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=
dcentric-minikube-m02   Ready    <none>          118m   v1.26.3   Node=B,beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=dcentric-minikube-m02,kubernetes.io/os=linux
dcentric-minikube-m03   Ready    <none>          118m   v1.26.3   Node=C,beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=dcentric-minikube-m03,kubernetes.io/os=linux
```


## Deployment

Redis can be deployed only in node A and node B

```bash
  helm install redis -n redis redis/ --create-namespace
```

Mongo can be deployed only in node B and node C, including create storage persistence

```bash
  kubectl apply -f mongo/mongo-tools-pv.yaml
  kubectl apply -f mongo/mongo-tools-pvc.yaml
  helm install mongo -n mongo mongo/ --create-namespace
```

Zookeeper can be deployed in all node except mongo node

```bash
  helm install zookeeper -n kafka zookeeper/ --create-namespace
```

Kafka can be deployed in all node except mongo node

```bash
  kubectl apply -f kafka/kafka-tools-pv.yaml
  kubectl apply -f kafka/kafka-tools-pvc.yaml
  helm install kafka -n kafka kafka/ --create-namespace
```

Optional, for monitoring stack

```bash
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  helm repo update
  helm install prometheus prometheus-community/kube-prometheus-stack
```

Kubernetes Prometheus Port Forward
```bash
  kubectl port-forward deployment/prometheus-grafana 3000
```

Log in to Grafana (default)
```bash
  username: admin
  password: prom-operator
```
---


## [Security Skill] Threat Modeling and Incident Response Plan
- Assemble Your Incident Response Team
  The cybersecurity incident will affects the entire company. It's necessary to include at least one dedicated person from each department you identify as crucial when dealing with the aftermath of the attack.

- Identify Vulnerabilities and Specify Critical Assets
  Specifying the most critical assets will allow the response team to prioritize their efforts in the event of an attack. Isolate the infected devices. Enable proxy/firewall, Change password, Enable/update password policy.

- Identify External Cybersecurity Experts and Data Backup Resources
  Check last backup, inform to all dev and product team we have recovery plan. Makesure automatic backup enable.

- Create a Detailed Response Plan Checklist
  Identification: Identify the breach.
  Containment: Contain what was attacked in order to isolate the threat.
  Eradication: Remove all threats from your devices and network.
  Recovery: Restore your system and network to their pre-incident state.
  Lessons Learned: Understand what errors were made and what steps need to be taken to curtail future attacks.

- Design a Communications Strategy
  Why it happen and how to prevent in the future. Make the security hardening for high priority task.