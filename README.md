# Stalwart Mail Helm Chart

```bash
$ helm install stalwart . -f your-values.yaml
```

## Values:

```yaml
replicaCount: 1

image:
  repository: stalwartlabs/mail-server
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""

persistence:
  enabled: true
  storageClass: ""
  accessMode: ReadWriteOnce
  size: 10Gi
  mountPath: /opt/stalwart
```

## TLS

One certificate, mounted at `/secrets/tls`:

```yaml
tls:
  enabled: true
  secretName: tls-incloudy-com-tr
```

Several certificates, one per domain, each mounted at its own path. Point the
Stalwart certificate config at the respective `tls.crt` / `tls.key`:

```yaml
tls:
  enabled: true
  secretName: tls-incloudy-com-tr      # mounted at /secrets/tls
  extraSecrets:
    - name: tls-ngu                     # volume name, must be unique
      secretName: tls-ngu-com-tr
      mountPath: /secrets/tls-ngu       # -> /secrets/tls-ngu/tls.crt|tls.key
    - name: tls-pottie
      secretName: tls-pottie-io
      mountPath: /secrets/tls-pottie
```

Every secret must be of type `kubernetes.io/tls`. Toggling `tls.enabled` or
changing `extraSecrets` requires a pod restart to pick up the new mounts.

## Resources

```
pod         stalwart-0
service     stalwart          LoadBalancer 8080/TCP,443/TCP,25/TCP,587/TCP,465/TCP,143/TCP,993/TCP,4190/TCP
service     stalwart-headless ClusterIP    8080/TCP,443/TCP,25/TCP,587/TCP,465/TCP,143/TCP,993/TCP,4190/TCP
statefulset stalwart
```
