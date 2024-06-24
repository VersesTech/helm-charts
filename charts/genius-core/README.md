# genius-core

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 2.35.0](https://img.shields.io/badge/AppVersion-2.35.0-informational?style=flat-square)

A Helm chart for Kubernetes

## TL;DR
```bash
helm install my-release oci://registry.develop.verses.io/helm-internal/genius-core
```

## Installation
To install the chart with the release name `my-release` in `my-namespace`:
```bash
helm -n my-namespace install my-release oci://registry.develop.verses.io/helm-internal/genius-core
```

## Configuration

### License
Genius Core must be configured with a proper license key and mTLS certificate bundle.

The following values show how to set the required parameters for the license and certificate:

```yaml
license:
  key: "YOUR_LICENSE_KEY"

  tls:
    cert: |-
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
    key: |-
      -----BEGIN PRIVATE KEY-----
      ...
      -----END PRIVATE KEY-----
    ca: |-
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
```

Alternatively, you can also reference existing secrets in the same namespace:

```yaml
license:
  keyExistingSecret:
    name: my-license-key-secret
    key: licenseKey

  tls:
    existingSecret:
      name: my-license-key-cert
      certKey: tls.crt
      keyKey: tls.key
      caKey: ca.crt
```

### Authentication
Authentication to Genius Core can be configured by setting variables to point to an existing OIDC provider.
The following parameters in the values file:
- `auth.issuerWellKnownUri`: Auth issuer URI for your auth provider (usually `<ISSUER_DOMAIN>/.well-known/openid-configuration`)
  - (alternatively) `auth.jwksUri`: JWKS URI for your auth provider
- `auth.defaultProvider`: Name of your auth provider
- `auth.initialAdminUserId`: The `sub` claim from the initial admin user's auth token/id token.

### Ingress
Since there are both gRPC and websocket protocols, two options have been provided for ingress resource creation:
- Use a single ingress that supports multiple backend protocols
- Use separate ingresses for each backend protocol

#### Single Ingress
If your ingress controller supports proxying to multiple backend protocols, you can set the following values to create a single ingress resource:

```yaml
ingress:
  enabled: true
  separateIngresses: false
  className: my-ingress-class
  hosts:
    - host: chart-example.local
      paths:
        - pathType: Prefix
          path: /
          backend:
            service:
              name: '{{ include "genius-core.fullname" . }}'
              port:
                number: '{{ .Values.service.ports.grpc }}'
    - host: chart-example-ws.local
      paths:
        - pathType: Prefix
          path: /
          backend:
            service:
              name: '{{ include "genius-core.fullname" . }}'
              port:
                number: '{{ .Values.service.ports.ws }}'
  tls:
    - secretName: chart-example-tls
      hosts:
        - chart-example.local
        - chart-example-ws.local
```

#### Separate Ingresses
If your ingress controller does not support proxying to multiple backend protocols, use the following values to create separate ingress resources:

```yaml
ingress:
  separateIngresses: true
  grpc:
    enabled: true
    className: nginx
    annotations:
      nginx.ingress.kubernetes.io/backend-protocol: "GRPC"
    hosts:
      - host: chart-example.local
        paths:
          - pathType: Prefix
            path: /
            backend:
              service:
                name: '{{ include "genius-core.fullname" . }}'
                port:
                  number:'{{ .Values.service.ports.grpc }}'
    tls:
      - secretName: chart-example-grpc-tls
        hosts:
          - chart-example.local
  ws:
    enabled: true
    className: nginx
    hosts:
      - host: chart-example-ws.local
        paths:
          - pathType: Prefix
            path: /
            backend:
              service:
                name: '{{ include "genius-core.fullname" . }}'
                port:
                  number: '{{ .Values.service.ports.ws }}'
    tls:
      - secretName: chart-example-ws-tls
        hosts:
          - chart-example-ws.local
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| allowedOrigins | string | `"*"` |  |
| auth.defaultProvider | string | `""` |  |
| auth.initialAdminUserId | string | `""` |  |
| auth.issuerWellKnownUri | string | `""` |  |
| auth.jwksUri | string | `""` |  |
| extraEnv | list | `[]` | Extra env vars to add to the container |
| extraObjects | list | `[]` | Extra K8s manifests to deploy # Note: Supports use of custom Helm templates |
| fullnameOverride | string | `""` |  |
| httpRoute.annotations | object | `{}` |  |
| httpRoute.enabled | bool | `false` |  |
| httpRoute.hostnames | list | `[]` |  |
| httpRoute.parentRefs | list | `[]` |  |
| httpRoute.rules[0].backendRefs[0].name | string | `"{{ include \"genius-core.fullname\" . }}"` |  |
| httpRoute.rules[0].backendRefs[0].port | string | `"{{ .Values.service.ports.grpc }}"` |  |
| httpRoute.rules[0].filters | list | `[]` |  |
| httpRoute.rules[0].matches[0].headers[0].name | string | `"host"` |  |
| httpRoute.rules[0].matches[0].headers[0].type | string | `"Exact"` |  |
| httpRoute.rules[0].matches[0].headers[0].value | string | `"chart-example.local"` |  |
| httpRoute.rules[0].matches[0].path.type | string | `"PathPrefix"` |  |
| httpRoute.rules[0].matches[0].path.value | string | `"/"` |  |
| httpRoute.rules[1].backendRefs[0].name | string | `"{{ include \"genius-core.fullname\" . }}"` |  |
| httpRoute.rules[1].backendRefs[0].port | string | `"{{ .Values.service.ports.ws }}"` |  |
| httpRoute.rules[1].filters | list | `[]` |  |
| httpRoute.rules[1].matches[0].headers[0].name | string | `"host"` |  |
| httpRoute.rules[1].matches[0].headers[0].type | string | `"Exact"` |  |
| httpRoute.rules[1].matches[0].headers[0].value | string | `"chart-example-ws.local"` |  |
| httpRoute.rules[1].matches[0].path.type | string | `"PathPrefix"` |  |
| httpRoute.rules[1].matches[0].path.value | string | `"/"` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"ghcr.io/versestech/helm-charts/genius-core"` |  |
| image.tag | string | `""` |  |
| imagePullSecrets | list | `[]` |  |
| ingress.annotations | object | `{}` |  |
| ingress.className | string | `""` |  |
| ingress.enabled | bool | `false` |  |
| ingress.grpc.annotations | object | `{}` |  |
| ingress.grpc.className | string | `""` |  |
| ingress.grpc.enabled | bool | `false` |  |
| ingress.grpc.hosts[0].host | string | `"chart-example.local"` |  |
| ingress.grpc.hosts[0].paths[0].backend.service.name | string | `"{{ include \"genius-core.fullname\" . }}"` |  |
| ingress.grpc.hosts[0].paths[0].backend.service.port.number | string | `"{{ .Values.service.ports.grpc }}"` |  |
| ingress.grpc.hosts[0].paths[0].path | string | `"/"` |  |
| ingress.grpc.hosts[0].paths[0].pathType | string | `"Prefix"` |  |
| ingress.grpc.tls | list | `[]` |  |
| ingress.hosts[0].host | string | `"chart-example.local"` |  |
| ingress.hosts[0].paths[0].backend.service.name | string | `"{{ include \"genius-core.fullname\" . }}"` |  |
| ingress.hosts[0].paths[0].backend.service.port.number | string | `"{{ .Values.service.ports.grpc }}"` |  |
| ingress.hosts[0].paths[0].path | string | `"/"` |  |
| ingress.hosts[0].paths[0].pathType | string | `"Prefix"` |  |
| ingress.hosts[1].host | string | `"chart-example-ws.local"` |  |
| ingress.hosts[1].paths[0].backend.service.name | string | `"{{ include \"genius-core.fullname\" . }}"` |  |
| ingress.hosts[1].paths[0].backend.service.port.number | string | `"{{ .Values.service.ports.ws }}"` |  |
| ingress.hosts[1].paths[0].path | string | `"/"` |  |
| ingress.hosts[1].paths[0].pathType | string | `"Prefix"` |  |
| ingress.separateIngresses | bool | `false` | Enable to create separate ingress resources for gRPC and websocket, since some ingress controllers do not support multiple backend protocols using a single ingress |
| ingress.tls | list | `[]` |  |
| ingress.ws.annotations | object | `{}` |  |
| ingress.ws.className | string | `""` |  |
| ingress.ws.enabled | bool | `false` |  |
| ingress.ws.hosts[0].host | string | `"chart-example-ws.local"` |  |
| ingress.ws.hosts[0].paths[0].backend.service.name | string | `"{{ include \"genius-core.fullname\" . }}"` |  |
| ingress.ws.hosts[0].paths[0].backend.service.port.number | string | `"{{ .Values.service.ports.ws }}"` |  |
| ingress.ws.hosts[0].paths[0].path | string | `"/"` |  |
| ingress.ws.hosts[0].paths[0].pathType | string | `"Prefix"` |  |
| ingress.ws.tls | list | `[]` |  |
| license.key | string | `""` | License key from onboarding portal |
| license.keyExistingSecret | object | `{"key":"licenseKey","name":""}` | Reference to an existing k8s secret |
| license.keyExistingSecret.key | string | `"licenseKey"` | Key inside secret that contains the secret content |
| license.keyExistingSecret.name | string | `""` | Name of the existing secret |
| license.tls | object | `{"ca":"","cert":"","existingSecret":{"caKey":"","certKey":"","keyKey":"","name":""},"key":""}` | Cerrtificates from onboarding portal |
| license.tls.ca | string | `""` | CA certificate from onboarding portal |
| license.tls.cert | string | `""` | Certificate from onboarding portal |
| license.tls.existingSecret | object | `{"caKey":"","certKey":"","keyKey":"","name":""}` | Reference to an existing k8s secret |
| license.tls.existingSecret.caKey | string | `""` | Key in secret that contains the secret content |
| license.tls.existingSecret.certKey | string | `""` | Key in secret that contains the secret content |
| license.tls.existingSecret.keyKey | string | `""` | Key in secret that contains the secret content |
| license.tls.existingSecret.name | string | `""` | Name of the existing secret |
| license.tls.key | string | `""` | Private key from onboarding portal |
| loglevel | string | `"info"` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| pdb.enabled | bool | `false` |  |
| pdb.maxUnavailable | string | `""` |  |
| pdb.minAvailable | string | `""` |  |
| persistence.accessModes[0] | string | `"ReadWriteOnce"` |  |
| persistence.size | string | `"10Gi"` |  |
| persistence.storageClassName | string | `""` |  |
| podAnnotations | object | `{}` |  |
| podSecurityContext.fsGroup | int | `65532` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| securityContext.runAsGroup | int | `65532` |  |
| securityContext.runAsNonRoot | bool | `true` |  |
| securityContext.runAsUser | int | `65532` |  |
| service.ports.grpc | int | `50052` |  |
| service.ports.ws | int | `8080` |  |
| service.type | string | `"ClusterIP"` |  |
| serviceAccount.annotations | object | `{}` |  |
| serviceAccount.create | bool | `true` |  |
| serviceAccount.name | string | `""` |  |
| serviceHostname | string | `"genius-core"` |  |
| tolerations | list | `[]` |  |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.13.1](https://github.com/norwoodj/helm-docs/releases/v1.13.1)
