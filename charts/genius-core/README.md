# genius-core

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 2.32.0](https://img.shields.io/badge/AppVersion-2.32.0-informational?style=flat-square)

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

### Authentication
Authentication to Genius Core can be configured by setting variables to point to an existing OIDC provider.
The following parameters in the values file:
- `auth.jwksUri`: JWKS URI for your auth provider
- `auth.defaultProvider`: Name of your auth provider
- `auth.initialAdminUserId`: The `sub` claim from the initial admin user's auth token/id token.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| allowedOrigins | string | `"*"` |  |
| auth.defaultProvider | string | `"auth0"` |  |
| auth.initialAdminUserId | string | `""` |  |
| auth.jwksUri | string | `"https://kosm-dev-verses.us.auth0.com/.well-known/jwks.json"` |  |
| auth.skipValidateJwtExpiry | bool | `false` |  |
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
| image.repository | string | `"518625631402.dkr.ecr.us-west-1.amazonaws.com/versestech/alexandria"` |  |
| image.tag | string | `""` |  |
| imagePullSecrets | list | `[]` |  |
| ingress.annotations | object | `{}` |  |
| ingress.className | string | `""` |  |
| ingress.enabled | bool | `false` |  |
| ingress.grpc | object | `{"annotations":{},"className":"","enabled":false,"hosts":[{"host":"chart-example.local","paths":[{"backend":{"service":{"name":"{{ include \"genius-core.fullname\" . }}","port":{"number":"{{ .Values.service.ports.grpc }}"}}},"path":"/","pathType":"Prefix"}]}],"tls":[]}` | Ingress configuration for gRPC ingress when `.Values.ingress.separateIngresses` is set to `true` |
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
| ingress.ws | object | `{"annotations":{},"className":"","enabled":false,"hosts":[{"host":"chart-example-ws.local","paths":[{"backend":{"service":{"name":"{{ include \"genius-core.fullname\" . }}","port":{"number":"{{ .Values.service.ports.ws }}"}}},"path":"/","pathType":"Prefix"}]}],"tls":[]}` | Ingress configuration for websocket ingress when `.Values.ingress.separateIngresses` is set to `true` |
| license.certs | object | `{"ca":"","cert":"","existingSecret":{"caKey":"","certKey":"","keyKey":"","name":""},"key":""}` | Cerrtificates from onboarding portal |
| license.certs.ca | string | `""` | CA certificate from onboarding portal |
| license.certs.cert | string | `""` | Certificate from onboarding portal |
| license.certs.existingSecret | object | `{"caKey":"","certKey":"","keyKey":"","name":""}` | Reference to an existing k8s secret |
| license.certs.existingSecret.caKey | string | `""` | Key in secret that contains the secret content |
| license.certs.existingSecret.certKey | string | `""` | Key in secret that contains the secret content |
| license.certs.existingSecret.keyKey | string | `""` | Key in secret that contains the secret content |
| license.certs.existingSecret.name | string | `""` | Name of the existing secret |
| license.certs.key | string | `""` | Private key from onboarding portal |
| license.key | string | `""` | License key from onboarding portal |
| license.keyExistingSecret | object | `{}` | Reference to an existing k8s secret |
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
| podSecurityContext.fsGroup | int | `1001` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| securityContext.runAsNonRoot | bool | `true` |  |
| securityContext.runAsUser | int | `1001` |  |
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
