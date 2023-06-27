# Dev Nation 2023

(this demo is based on the [Multi-cluster Cloud-Native grpc (microservices) application demo](https://github.com/skupperproject/skupper-example-grpc)


## Step 1: Install Skupper in public clusters 
Public1 namespace:
```
kubectl apply -f public1-cluster/deployment-ms-a.yaml
skupper init
``` 
Public 2 namespace:
```
kubectl apply -f public2-cluster/deployment-ms-b.yaml
skupper init
```


## Step 2: Install Skupper in a private site enabling the console
Private 1 namespace
``` 
kubectl apply -f private1-cluster/deployment-ms-c.yaml
skupper init --enable-flow-collector --enable-console --console-user admin --console-password 1234 --console-auth internal
```

## Step 3: Create tokens
Public1 namespace
```
skupper token create public1-token.yaml --uses 2
```

Public2 namespace
```
skupper token create public2-token.yaml
``` 


## Step 4: Create the links to establish the VAN

Public2 namespace
```
skupper link create ~/public1-token.yaml
```

Private 1 namespace
```
skupper link create ~/public1-token.yaml 
skupper link create ~/public2-token.yaml
```

## Step 5: Look at the link status
```
skupper link status 
```

```
skupper link status --show-incoming-links 
```

## Step 6: Exposing services through Skupper
Public 1 namespace:
```
./public1-cluster/expose-deployments-a.sh
```

Public 2 namespace:
```
./public2-cluster/expose-deployments-b.sh
```

Private 1 namespace:
```
./private1-cluster/expose-deployments-c.sh
```

## Step 7: Look at the status

skupper status

## Step 8: Look at the console

Go to the link that shows "skupper status command" in the private namespace

## Step 9: Check that it works
```
kubectl get service/frontend-external
/usr/bin/firefox --new-window  "http://$(kubectl get service frontend-external -o=jsonpath='{.spec.clusterIP}')/"
```

## Step 10: Revoke access
Public 1 namespace
```
skupper revoke-access
```

## Step 11: Debug events
```
skupper debug events
```
## Step 12: Debug events in kubernetes
```
kubectl get events
```
