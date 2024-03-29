# DC/OS Ingress using Edge-LB

- When using multiple pools, it is recommended to use different `bindport` for each pool so multiple pools can coexist on the same public node.
- If using Terraform to create a cluster, you can use the `public_agents_additional_ports` parameters to create the cluster with the necessary ports open. [Terraform documentation](https://registry.terraform.io/modules/dcos-terraform/dcos/aws/0.1.7?tab=inputs)
- [EdgeLB Service/Ingress controller for Kubernetes](https://github.com/mesosphere/dklb)


### Sample Edge-LB Config files for DC/OS services.

- [MJS - Mesosphere Jupyter Service](jupyterlab-edgelb.json) - `https://<edgelb ip>:8888/jupyterlab-notebook`
- [Kubernetes](k8s-edgelb.json)
  - This assumes a K8s services named `k8s`.  If you have a service with a different name, update the property `frameworkName` with the name of your service in the Edge-LB config file (line 26).
   ```bash
   dcos kubernetes cluster kubeconfig \
       --insecure-skip-tls-verify \
       --context-name=k8s \
       --cluster-name=k8s \
       --apiserver-url=https://<edgelb ip>:6443/
   ```
  - There are two ways of exposing K8s dashboard without the need for `kubectl proxy`.
    - When you have a public K8s node, you can use nodeports.  Using [dashboard-ext-nodeport.yaml](dashboard-ext-nodeport.yaml) the dashboard will be available at `https://<K8 public node ip>:30443/`.  **Not working with version `2.2.1-1.13.4`**.
    - Using [dklb](https://github.com/mesosphere/dklb) and  [dashboard-ext-dklb.yaml](dashboard-ext-dklb.yaml) the dashboard will be available at `https://<edgelb ip>/`.
- [Monitoring](monitoring-edgelb.json) - Exposes Grafana and Prometheus
  - Grafana - `https://<edgelb ip>:3000/`
- [Jenkins](jenkins-edgelb.json) - Exposes Jenkins using EdgeLB
  - Jenkins - `https://<edgelb ip>:10443/`

