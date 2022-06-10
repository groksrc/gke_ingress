Note that GKE L7 ELBs do not support url rewriting. Using the nginx ingress controller is required.

Installed nginx on GKE with the standard install steps:
https://kubernetes.github.io/ingress-nginx/deploy/#gce-gke

Note the warning on Private Clusters which applied in my case.
In order to use nginx on a private cluster, the control plane must be able to
contact the nodes on port 8443.

$ gcloud compute firewall-rules create gke-nginx-ingress \
    --description "Allow GKE Control Plane to access nginx-ingress controller" \
    --action allow \
    --rules tcp:8443 \
    --network <your network> \
    --project <your project> \
    --source-ranges <your master control plane range> \
    --target-tags <your target tags>

For more on nginx rewrite rules:
https://github.com/kubernetes/ingress-nginx/blob/main/docs/examples/rewrite/README.md
