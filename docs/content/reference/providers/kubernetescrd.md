# Kubernetes -- Reference

## Kubernetes

```yaml
################################################################
# Kubernetes Provider
################################################################

apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: ingressroutes.traefik.containo.us

spec:
  group: traefik.containo.us
  version: v1alpha1
  names:
    kind: IngressRoute
    plural: ingressroutes
    singular: ingressroute
  scope: Namespaced

---
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: middlewares.traefik.containo.us
spec:
  group: traefik.containo.us
  version: v1alpha1
  names:
    kind: Middleware
    plural: middlewares
    singular: middleware
  scope: Namespaced

---
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: ingressroute.crd
spec:
  entrypoints:
    - web
    - web-secure
  routes:
  - match: Host(`foo.com`) && PathPrefix(`/bar`)
    kind: Rule
    priority: 12
    # defining several services is possible and allowed, but for now the servers of
    # all the services (for a given route) get merged altogether under the same
    # load-balancing strategy.
    services:
    - name: s1
      port: 80
      healthcheck:
        path: /health
        host: baz.com
        intervalseconds: 7
        timeoutseconds: 60
      # strategy defines the load balancing strategy between the servers. It defaults
      # to Round Robin, and for now only Round Robin is supported anyway.
      strategy: RoundRobin
    - name: s2
      port: 433
      healthcheck:
        path: /health
        host: baz.com
        intervalseconds: 7
        timeoutseconds: 60
  - match: PathPrefix(`/misc`)
    services:
    - name: s3
      port: 80
    middleware:
    - name: stripprefix
    - name: addprefix
  tls:
    secretName: supersecret
```