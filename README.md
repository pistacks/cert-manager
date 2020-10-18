# cert-manager
Cert Manager for ARM

## Usage

Create the namespace:

```
$ kubectl create namespace cert-manager
```

The manifest in this repository is from this [source](https://github.com/jetstack/cert-manager/releases/download/v0.11.0/cert-manager.yaml), only the images has been modified to `-arm` to enable it to work for arm. You can either use this manifest, or the original and just update the image names to work:

```
$ kubectl apply -f https://raw.githubusercontent.com/pistacks/cert-manager/main/v0.11.0/cert-manager.yaml
```

Verify that it's running:

```
$ $ kubectl get pods --namespace cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-cainjector-75f88c9f56-zl2dw   1/1     Running   0          25s
cert-manager-77d8f4d85f-pnvxs              1/1     Running   0          25s
cert-manager-webhook-56669d7fcb-sj9n2      1/1     Running   1          25s
```

ClusterIssuer resource:

```
$ cat letsencrypt.yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: <your>@<email>.<com> # replace this
    privateKeySecretRef:
      name: prod-issuer-account-key
    server: https://acme-v02.api.letsencrypt.org/directory
    http01: {}
    solvers:
      - http01:
          ingress:
            class: traefik
        selector: {}
```

Apply to Kubernetes:

```
$ kubectl apply -f letsencrypt.yml
```

Verify:

```
$ kubectl describe clusterissuer letsencrypt
```

Use it in your ingress:

```
$ cat ingress.yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myapp-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: traefik
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
    - secretName: myapp-mydomain-com-tls
      hosts:
        - myapp.mydomain.com
  rules:
  - host: myapp.mydomain.com
    http:
      paths:
        - path: /
          backend:
            serviceName: myapp-service
            servicePort: 80
```
