## About

This project is for testing a Prow deployment for BU EC528.  

This repo uses a GitHub App, Google Kubernetes Engine, and Google Cloud Storage.


## Setup

Primarily followed these instructions: https://docs.prow.k8s.io/docs/getting-started-deploy/#update-the-sample-manifest



## Relaunching and Troubleshooting

 - Log into GKE (change <PROJECT_NAME>): https://console.cloud.google.com/kubernetes/list/overview?project=<PROJECT_NAME>
 - Activate Google Cloud Shell (terminal icon in upper-right)
 - Run the following commands to reconfigure kubectl to your cluster and check the pod statuses: 

   ```console
   gcloud container clusters get-credentials <CLUSTER_NAME> --zone <ZONE> --project <PROJECT_ID>
   # e.g. gcloud container clusters get-credentials prow --zone us-east1-b --project kata-prow
   ```

   ```console
   kubectl get pods -n prow
   ```

   Should return something like:

    | NAME                                           | READY   | STATUS    | RESTARTS      | AGE |
    | ---------------------------------------------- | ------- | --------- | ------------- | --- |
    | crier-6b44d56475-ctp6s                         | 1/1     | Running   | 0             | 9h  |
    | deck-6fd7598568-pzs59                          | 1/1     | Running   | 0             | 9h  |
    | deck-6fd7598568-xq8mx                          | 1/1     | Running   | 0             | 9h  |
    | ghproxy-c88848bf4-dj5mk                        | 1/1     | Running   | 0             | 10h |
    | hook-78c785b9cc-bg8v9                          | 1/1     | Running   | 0             | 9h  |
    | hook-78c785b9cc-lr8tm                          | 1/1     | Running   | 0             | 9h  |
    | horologium-785784f799-wpst9                    | 1/1     | Running   | 0             | 10h |
    | prow-controller-manager-5965d9fbb8-nlqct       | 1/1     | Running   | 0             | 10h |
    | sinker-5dfc5cd768-qlw7j                        | 1/1     | Running   | 0             | 10h |
    | statusreconciler-78866d9f4c-25cgz              | 1/1     | Running   | 11 (9h ago)   | 10h |
    | tide-5b6dc87fc9-xmmb9                          | 1/1     | Running   | 2 (25s ago)   | 26s |


   Troubleshooting commands to get all kubernetes resources, restart deployments, or review logs:

   ```console
   kubectl get all -n prow
   kubectl rollout restart deployment <deployment-name> -n prow
   kubectl logs deployment/<deployment-name> -n prow
   ```

  If only tide is failing, try the following:

  ```console
  echo "[]" > tide-history.json
  gsutil cp tide-history.json gs://prow-logs-22622/tide-history.json
  kubectl rollout restart deployment <deployment-name> -n prow
  kubectl get pods -n prow
  ```


### Other useful info

Apply an updated manifest (e.g. starter-gcs.yaml) via:

```console
kubectl apply -f starter-gcs.yaml
```

To get the External IP:

```console
kubectl get service deck -n prow
```


### Files

The following files should be setup in the bucket:

 - gs://prow-logs-22622/status-reconciler-status
 - gs://prow-logs-22622/tide-history.json
 - gs://prow-logs-22622/tide-status
 - gs://prow-logs-22622/logs/

All can be empty arrays, except the json, which is empty curly braces. 

The following key files should exist in the cloud instance: 
TODO: add more detail...
 - secret (generated via openssl command and added to GitHub App workflow secret)
 - 528-prow-test.2024-11-18.private-key.pem generated from within the GitHub App
 - key.json generated in Google Cloud
 - gcs-credentials.json generated in Google Cloud

Pull a config file or secret: 

```console
kubectl get configmap config -n prow -o yaml > config.yaml
kubectl get deployment plank -n prow -o yaml > plank.yaml
kubectl get secret kubeconfig -n prow -o yaml > secret.yaml
```

Apply a config file:
```console
kubectl apply -f config.yaml -n prow
```

Restart a service:

```console
kubectl rollout restart deployment/plank -n prow
```

Scale replicas

```console
kubectl scale deployment plank --replicas=1 -n prow
```

Delete a pod
```console
kubectl delete pod -l app=plank -n prow
```

Browse pod files
```console
kubectl exec -n prow deck-5f7b47985d-f9g9b -- ls etc/config -la
```

Search kubernetes configs, configmaps, and secrets
```console
kubectl get all --all-namespaces -o yaml | grep -E '35\.243\.234\.190'
kubectl get configmap --all-namespaces -o yaml | grep -E '35\.243\.234\.190'
kubectl get secrets --all-namespaces -o yaml | grep -E '35\.243\.234\.190'
```

kubectl set image deployment/plank -n prow plank=gcr.io/k8s-prow/plank:v20210410-57fae234ba
https://console.cloud.google.com/gcr/images/k8s-prow/GLOBAL/plank?inv=1&invt=Abh9Nw



plank now running, but still getting the tide error in GH
May need to update ip address (kubectl get service deck -n prow) in GH webhook, GH App, config file and plank file.
GPT seems at a dead end.  May need ot start fresh and walk through all configs, etc slowly.


COMMAND HELP
https://prow.k8s.io/command-help


https://github.com/kubernetes/test-infra/issues/24141



Isaac Guerreiro to Everyone (Nov 26, 2024, 2:18 PM)
https://teselagen.com/molecular-biology-toolkit/
https://teselagen.com/wp-content/uploads/2024/09/DNA-assembly-design-1536x1032.png
 
Isaac Guerreiro to Everyone (Nov 26, 2024, 2:32 PM)
We can have a short meeting with all devs so we discuss this Nick
 
Isaac Guerreiro to Everyone (Nov 26, 2024, 2:41 PM)
We can take a highly suspicious sequence like the spike protein, take a very small part of the sequence (which will be hard to trigger warning on biosecurity checks) and use for the deletions examples.
 
Isaac Guerreiro to Everyone (Nov 26, 2024, 2:48 PM)
Create a UI maybe?
 
Isaac Guerreiro to Everyone (Nov 26, 2024, 2:54 PM)
We can search something like "snakemake pipeline protein design", so we can see each tool and step used for protein design
 
Isaac Guerreiro to Everyone (Nov 26, 2024, 3:16 PM)
 Will be interesting to send sequences directly from the twist workflow page that is not feasible, with annotations to Benchling and Geneious Prime
 
Isaac Guerreiro to Everyone (Nov 26, 2024, 3:23 PM)
What do you think about nail down a demo for Benchling + Twist?

709 E 5th St.
Boston, MA 02127