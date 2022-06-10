# GKE Container Native Loadbalancing With Fanout Ingress
This repo demonstrates how to configure a single ingress to support
multiple app deployments in a simple fanout configuration.

Note that GKE L7 ELBs do not support url rewriting. Using the nginx ingress controller is required.

Also, nginx is used twice here so don't let that confuse you. The nginx deployment is not the nginx-ingress. The deployment is just a dummy container used as the endpoint for the fanout.

## Steps

## 1. Install The nginx ingress controller on the cluster

Install nginx on GKE with the standard install steps:<br>
https://kubernetes.github.io/ingress-nginx/deploy/#gce-gke

## 2. Apply Firewall Rules
Note the warning on Private Clusters which applied in my case.
In order to use nginx on a private cluster, the control plane must be able to
contact the nodes on port 8443.

```
$ gcloud compute firewall-rules create gke-nginx-ingress \
    --description "Allow GKE Control Plane to access ingress-nginx-controller" \
    --action allow \
    --rules tcp:8443 \
    --network <your network> \
    --project <your project> \
    --source-ranges <your master control plane range> \
    --target-tags <your target tags>
```

## 3. Install The Deployments, Services, and Ingress
```
$ kubectl apply -f deployment-hello-app.yaml
$ kubectl apply -f deployment-nginx-yaml
$ kubectl apply -f service-hello-app.yaml
$ kubectl apply -f service-nginx.yaml
$ kubectl apply -f ingress.yaml
```

## 4. Test
* Find the IP Address of the ingress
* `$ curl <ip address>` should report back from the Hello World app
* `$ curl <ip address>/nginx` should report back from the nginx container.


Note there could be multiple other paths defined on the fanout, but instead we use the defaultBackend to fall back.

For more on nginx rewrite rules: <br>
https://github.com/kubernetes/ingress-nginx/blob/main/docs/examples/rewrite/README.md
