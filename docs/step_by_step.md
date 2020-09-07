# Auto scaling with Kubernetes

Example folder for this training : `examples/nginx_auto_scaler`

## Install Kustomize binary

For MacOS users

```
brew install kustomize
```

## Create a kapsule cluster

Configurations from Scaleway console

```
dev1m instance size
3 nodes
autoscale on (1-10)
advanced options
no dashboard (or as you want)
```

## Configure nginx deployment

In `nginx-deployment.yaml`, define the wanted configuration

```
cat > nginx-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:alpine
          resources:
            requests:
              cpu: "2.5"
            ports:
              - containerPort: 80
                name: http
EOF
```

## Create kustomize file

```
cat > kustomization.yml <<EOF
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- 'https://github.com/jetstack/cert-manager/releases/download/v0.16.1/cert-manager.yaml'
- cluster-issuer.yml
EOF
```

## Configure cluster-issuer

```
cat > cluster-issuer.yml <<EOF
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory #https://acme-v02.api.letsencrypt.org/directory
    email: <YOUR_EMAIL>
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
EOF
```

And then build

`kustomize build | kubectl apply -f -`