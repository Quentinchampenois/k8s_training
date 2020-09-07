# Proceed step by step 

## Create a kapsule cluster

Configurations from Scaleway console

```
dev1m instance size
3 nodes
autoscale off
advanced options
no dashboard (or as you want)
```

Then download the kubeconfig file. You can find it in the Scaleway console.

On your local machine, unzip the kubeconfig file and store it as `config` file in `~/.kube` folder


For MacOS users 

```
cd <CURRENT_PROJECT_FOLDER_PATH>
unzip <YOUR_FILENAME>.zip
mv <YOUR_FILENAME>.yaml ~/.kube/config
```


## Access dashboard

Run `kubectl proxy`

Access dashboard with following url : `http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login`

Choose token authentication and use the one specified in `~/.kube/config`

## Deploy nginx ingress controller

To deploy an NGINX ingress controller, we can proceed with several methods.

In this case, we will use the yaml configuration.

Create new `nginx-deployment.yaml` file

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
EOF
```

Apply the current configuration 

```
kubectl apply -f nginx-deployment.yaml
```

You should see new pods running using `kubectl get pods` command

Finally, you can port forward the current nginx to be able to access it

```
kubectl port-forward <NGINX_POD_NAME> <YOUR_LOCAL_PORT>:80
```
