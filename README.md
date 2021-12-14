# ACM Dashboard for ArgoCD Deployments


## Requirements
* [OpenShift](https://www.openshift.com/)
* [Red Hat Advanced Cluster Management for Kubernetes](https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.4)
*  [Observability service](https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.4/html/observability/observing-environments-intro#enable-observability)
* Ensure metric is enables on argocd operator

## Getting started 
**Once ACM is installed run the following commands to install the Observability service**  
* [Install ACM observability Service](install-acm-observability-service.md)

**Install the Grafana dev environment on cluster**
* [How to design a grafana dashboard](https://github.com/open-cluster-management/multicluster-observability-operator/tree/main/tools)


**Once the e Grafana dev enviornment is installed you can run the below command to access the UI** 
```
echo https://multicloud-console.$(oc get ingresses.config.openshift.io cluster -o jsonpath='{ .spec.domain }')/grafana-dev/
```

**Create observability-metrics-custom-allowlist.yaml**
```
cat >observability-metrics-custom-allowlist.yaml<<YAML
kind: ConfigMap
apiVersion: v1
metadata:
  name: observability-metrics-custom-allowlist
data:
  metrics_list.yaml: |
    names:
      - argocd_cluster_info
      - argocd-server-metrics
      - argocd_app_info
      - argocd_app_sync_total
      - argocd_app_reconcile_count
      - argocd_app_reconcile_bucket
      - argocd_app_k8s_request_total
      - argocd_kubectl_exec_pending
      - argocd-metrics
      - argocd_cluster_api_resource_objects
      - argocd_cluster_api_resources
      - argocd_git_request_total
      - argocd_git_request_duration_seconds_bucket
      - argocd-repo-server
      - argocd_redis_request_total
YAML
```

**Apply config map against RHACM**
```
oc apply -n open-cluster-management-observability -f observability-metrics-custom-allowlist.yaml
```

## Dashboard List 
> All the command beow need to be ran against the RHACM cluster in order to be shown on the grafana dashboard. 

### Load argocd dashboard
```
oc create -f config-files/argocd-dashboard.yaml
```