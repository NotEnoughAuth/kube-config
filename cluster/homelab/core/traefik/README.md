Must add a SECRETS configuration

apiVersion: v1
kind: Secret
metadata:
name: traefik-dashboard-auth
namespace: traefik
type: Opaque
data:
users: abc123==

Create user/pass with the below command

htpasswd -nb user password | openssl base64

Also must install traefik with the below values.yml

globalArguments: - "--global.sendanonymoususage=false" - "--global.checknewversion=false"
additionalArguments: - "--serversTransport.insecureSkipVerify=true" - "--log.level=INFO"
deployment:
enabled: true
replicas: 3
annotations: {}
podAnnotations: {}
additionalContainers: []
initContainers: []
ports:
web:
redirectTo:
port: websecure
websecure:
tls:
enabled: true
ingressRoute:
dashboard:
enabled: false
providers:
kubernetesCRD:
enabled: true
ingressClass: traefik-external
allowExternalNameServices: true
kubernetesIngress:
enabled: true
allowExternalNameServices: true
publishedService:
enabled: false
rbac:
enabled: true
