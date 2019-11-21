# w8keMeUP

Spawn kubernetes resources when needed.

## Goal

Some controllers only have to run when resources are instanciated.

This controller watch for resources and instanciate correspoding controller when needed.

## Usage

_w8keMeUP_ use [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) to be configured.

- `spec.watch`: indicate which resources to watch. If any match, it triggers _scale_ and _create_.
- `spec.scale`: resources to scale to `replicas`.
- `spec.create`: resources to create. 

## example

```yaml
apiVersion: w8kemeup.barpilot.io/v1alpha1
kind: ScaleOrCreate
metadata:
  name: ingress-controller
spec:
  watch:
  - apiVersion: networking.k8s.io/v1beta1
    resource: ingress
  scale:
  - resource:
      apiVersion: apps/v1
      resource: deployment
      labelSelector:
        matchLabels:
          app: nginx
          component: frontend
    replicas: 2
    scaleToZero: true
  create:
  - inline: |
      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
      spec:
        selector:
          app: nginx
          component: frontend
        ports:
        - port: 80
          targetPort: 80
        type: LoadBalancer
```