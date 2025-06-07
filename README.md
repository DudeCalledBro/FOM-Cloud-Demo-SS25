# Cloudtechnologie Demo (2025-SS)

In dieser kurzen Demo werde ich ein lokales K3s Kubernetes Cluster mithilfe von Vagrant und Ansible installieren. Als Provisioner für die virtuellen Maschinen verwende ich Parallels Desktop.

Am Ende dieser Demo haben wir ein kleines Kubernetes Cluster installiert, welches aus einer Control Plane Node und zwei Worker Nodes besteht. Die Installation weiterer Control Plane Nodes macht es komplexer und wird deshalb nicht gezeigt.

## Schritt-für-Schritt

1. Im ersten Schritt starten wir die drei virtuellen Maschinen. Diese habe ich bereits im Vagrant-Manifest vorab konfiguriert (Hostname, Netzwerk und Post-Provisioning). Die Maschinen werden nach Abschluss der Installation noch mit Ansible grundlegend konfiguriert, sodass ein Benutzer und ein autorisierter SSH-Key hinterlegt wird.

    ```bash
    # Start Virtual Machine Setup
    vagrant up

    # Check Virtual Machine Setup
    ansible -m ping all
    ```

2. Als nächstes wird die eigentliche Control Plane Node, im K3s-Umfeld auch `Server` genannt, installiert. In unserem Fall ist dies `k3s-01`. Hierzu führe ich ein weiteres Ansible-Playbook aus, was die Installation auf dem System durchführt. In diesem Schritt wird außerdem noch MetalLB als Loadbalancer mittels HelmChart installiert.

    ```bash
    # Install Control Plane
    ansible-playbook k3s_server.yml

    # Check K3s Cluster Status
    ansible -m command -a "kubectl get nodes" server
    ```

3. Im vorletzten Schritt werden weitere Worker Nodes dem Cluster hinzugefügt. Diese werden im K3s-Kontext auch `Agents` genannt. Die Installation erfolgt auch hier mittels Ansible.

    ```bash
    # Install Worker Nodes
    ansible-playbook k3s_agent.yml

    # Check K3s Cluster Status
    ansible -m command -a "kubectl get nodes" server
    ```

4. Der Cluster ist nun soweit installiert und die verschiedenen Pods sollten nun aufgelistet werden.

    ```bash
    # Check K3s Cluster Pods
    ansible -m command -a "kubectl get pods -A" server
    ```

## Demo-App

Zur Prüfung der Funktionalität des Clusters, habe ich ein kleines NGINX-Deployment vorbereitet. Dieses ist zwar nicht besonders hübsch, oder sinnvoll aufgebaut (z.B. Nutzung des Ingress-Controllers). Dafür zeigt es schön, wie eine Applikation im Browser aufgerufen werden kann.

```bash
# Create Example App Manifests
ansible-playbook example.yml

# Connect to Control Plane Node
vagrant ssh k3s-01

# Create Example App
kubectl apply -f /home/vagrant/fom-demo

# Get Pods
kubectl get pods -n fom

# Scale Kubernetes Pods
kubectl scale -n fom --replicas=3 deployment/nginx
```

## License

Copyright © 2025 Niclas Spreng

Licensed under the [MIT license](LICENSE).
