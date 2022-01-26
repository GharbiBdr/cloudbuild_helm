# cloudbuild_helm


gcloud iam service-accounts create connectors


gcloud projects add-iam-policy-binding sandbox-334514 \
    --member="serviceAccount:connectors@sandbox-334514.iam.gserviceaccount.com" \
    --role="roles/owner"


gcloud iam service-accounts add-iam-policy-binding \
connectors@sandbox-334514.iam.gserviceaccount.com \
    --member="serviceAccount:sandbox-334514.svc.id.goog[cnrm-system/cnrm-controller-manager]" \
    --role="roles/iam.workloadIdentityUser"


kubectl apply -f configconnector.yaml


kubectl create namespace sidespin


kubectl annotate namespace \
sidespin cnrm.cloud.google.com/project-id=sandbox-334514

### wait
kubectl wait -n cnrm-system \
      --for=condition=Ready pod --all


### test permission and troubleshooting
kubectl apply -f wi-test.yaml

kubectl exec -it workload-identity-test \
  --namespace cnrm-system \
  -- /bin/bash

kubectl delete pod workload-identity-test \
--namespace cnrm-system


### enale api
gcloud services enable serviceusage.googleapis.com


### To see what kinds of Google Cloud resources you can create with Config Connector, run:
kubectl get crds --selector cnrm.cloud.google.com/managed-by-kcc=true

#### For example, you can view the API description for the PubSubTopic resource with kubectl describe:
kubectl describe crd pubsubtopics.pubsub.cnrm.cloud.google.com

### create pubsub-topic
kubectl apply -f pubsub-topic.yaml 
