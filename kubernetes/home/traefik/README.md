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
