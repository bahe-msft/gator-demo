## **unsupported kustomization.yaml** (missing gvk)

```
$ gator test --filename manifests/nginx-ingress-kind
auditing objects: adding data of GVK "/, Kind=": admission.k8s.gatekeeper.sh: invalid request object: resource  has no version
```

## `gatekeeper-library` is checking on pod level

```
$ gator test --filename manifests/nginx-ingress-kind/deploy.yaml --filename constraint.yaml --filename host-networks.yaml
# no error
```

```
$ diff host-networks.yaml host-networks-controller-aware.yaml 
91a92,96
> 
>         input_containers[c] {
>             c := input.review.object.spec.template.spec.containers[_]
>             not is_exempt(c)
>         }
$ gator test --filename manifests/nginx-ingress-kind/deploy.yaml --filename constraint.yaml --filename host-networks-controller-aware.yaml
apps/v1/Deployment ingress-nginx/ingress-nginx-controller: ["psp-host-network-ports"] Message: "The specified hostNetwork and hostPort are not allowed, pod: ingress-nginx-controller. Allowed values: {\"hostNetwork\": true, \"max\": 9000, \"min\": 1024}"
```