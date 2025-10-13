Step 1: Create the Namespace

# Create the dev namespace
```
kubectl create namespace dev
```

![alt text](image.png)

Step 2: Create the Nginx Pod in the dev Namespace

```
# Create the nginx pod in the dev namespace
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80
EOF
```

![alt text](image-1.png)

Step 3: Verification
Check if the namespace was created:
```
kubectl get namespaces | grep dev
```

![alt text](image-2.png)

Check if the pod is running in the dev namespace:
```
kubectl get pods -n dev
```

![alt text](image-3.png)

Get detailed information about the pod:
```
kubectl describe pod dev-nginx-pod -n dev
```

![alt text](image-4.png)

Check the pod logs to ensure nginx started correctly:
```
kubectl logs dev-nginx-pod -n dev
```

![alt text](image-5.png)