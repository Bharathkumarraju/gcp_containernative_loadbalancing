# gcp_containernative_loadbalancing
Container Native Loadbalancing

## VPC and subnets in bharath

### Project: bharathpoc


| Name                  | IP Range                       | Region/GKE     |   VPC                      | Private GKE Master IP range                     |
| --------------------- | ------------------------------ | -------------- | -------------------------- | ----------------------------------------------- |
| google-managed-services-allocated-range| 10.180.0.0/20 | asia-southeast1|   bharathpoc-vpc    |                                                 |
| sg-bharathpoc-subnet1 | 10.180.16.0/24 | asia-southeast1|   bharathpoc-vpc    |                                                                 |    
| sg-bharathpoc-subnet1 | 172.16.0.0/20(GKE Containers & 172.16.16.0/20(GKE Services) | asia-southeast1|   bharathpoc-vpc  | 192.168.0.0/28       |
| sg-bharathpoc-subnet2 | 10.180.17.0/24  | asia-southeast1|   bharathpoc-vpc    |                                                                |
| sg-bharathpoc-subnet2 | 172.16.32.0/20(GKE Containers) & 172.16.48.0/20(GKE Services) | asia-southeast1| bharathpoc-vpc  | 192.168.0.16/28      |

![VPC in Google Console](./images/vpc_subnet.png)


### Create GKE CLuster

```
MacBook-Pro:gcp_containernative_loadbalancing bharathdasaraju$ gcloud beta container clusters create "gkeneg-demo" \
    --project "bharathpoc" \
    --zone "asia-southeast1-a" \
    --machine-type "e2-medium" --image-type "COS" --disk-type "pd-standard" --disk-size "100" \
    --metadata disable-legacy-endpoints=true \
    --num-nodes "2" \
    --enable-private-nodes --enable-private-endpoint --master-ipv4-cidr "192.168.0.0/28" \
    --enable-ip-alias \
    --network "projects/bharathpoc/global/networks/bharathpoc-vpc" \
    --subnetwork "projects/bharathpoc/regions/asia-southeast1/subnetworks/sg-bharathpoc-subnet1" \
    --cluster-secondary-range-name "containers" \
    --services-secondary-range-name "services" \
    --no-enable-intra-node-visibility \
    --default-max-pods-per-node "16" \
    --enable-autoscaling \
    --min-nodes "2" --max-nodes "3" --enable-master-authorized-networks \
    --addons HorizontalPodAutoscaling,HttpLoadBalancing,NodeLocalDNS,GcePersistentDiskCsiDriver \
    --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 \
    --maintenance-window-start "2021-06-16T16:00:47Z" --maintenance-window-end "2021-06-17T16:00:47Z" --maintenance-window-recurrence "FREQ=WEEKLY;BYDAY=SA,SU" \
    --enable-shielded-nodes \
    --disable-default-snat \
    --node-locations "asia-southeast1-a","asia-southeast1-b","asia-southeast1-c"
Note: The Pod address range limits the maximum size of the cluster. Please refer to https://cloud.google.com/kubernetes-engine/docs/how-to/flexible-pod-cidr to learn how to optimize IP address allocation.
Creating cluster gkeneg-demo in asia-southeast1-a... Cluster is being health-checked (master is healthy)...done.
Created [https://container.googleapis.com/v1beta1/projects/bharathpoc/zones/asia-southeast1-a/clusters/gkeneg-demo].
To inspect the contents of your cluster, go to: https://console.cloud.google.com/kubernetes/workload_/gcloud/asia-southeast1-a/gkeneg-demo?project=bharathpoc
kubeconfig entry generated for gkeneg-demo.
NAME         LOCATION           MASTER_VERSION   MASTER_IP    MACHINE_TYPE  NODE_VERSION     NUM_NODES  STATUS
gkeneg-demo  asia-southeast1-a  1.21.6-gke.1503  192.168.0.2  e2-medium     1.21.6-gke.1503  6          RUNNING
MacBook-Pro:gcp_containernative_loadbalancing bharathdasaraju$
```

