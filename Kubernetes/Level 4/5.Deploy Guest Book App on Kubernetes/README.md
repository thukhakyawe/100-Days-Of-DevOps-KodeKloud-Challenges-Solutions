## Step 1: Create the Redis Master Deployment

```
cat > redis-master-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-xfusion
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
        - containerPort: 6379
EOF

# Deploy redis-master-deployment.yaml
kubectl apply -f redis-master-deployment.yaml
```


## Step 2: Create the Redis Master Service

```
cat > redis-master-service.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis-master
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis-master
EOF

# Deploy redis-master-service.yaml
kubectl apply -f redis-master-service.yaml
```



## Step 3: Create the Redis Slave Deployment

```
cat > redis-slave-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
  labels:
    app: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-xfusion
        image: gcr.io/google_samples/gb-redisslave:v3
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
        ports:
        - containerPort: 6379
EOF

# Deploy redis-slave-deployment.yaml
kubectl apply -f redis-slave-deployment.yaml
```

## Step 4: Create the Redis Slave Service

```
cat > redis-slave-service.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis-slave
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis-slave
EOF

# Deploy redis-slave-service.yaml
kubectl apply -f redis-slave-service.yaml
```

## Step 5: Create the Frontend Deployment

```
cat > frontend-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-xfusion
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
        ports:
        - containerPort: 80
EOF

# Deploy frontend-deployment.yaml
kubectl apply -f frontend-deployment.yaml
```

## Step 6: Create the Frontend Service (NodePort)

```
cat > frontend-service.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
  selector:
    app: frontend
EOF

# Deploy frontend-service.yaml
kubectl apply -f frontend-service.yaml
```

## Step 7: Verify the Setup

Let's run comprehensive checks to ensure everything is set up correctly:

```
# Check all deployments
echo "=== Checking Deployments ==="
kubectl get deployments

# Check all services
echo -e "\n=== Checking Services ==="
kubectl get services

# Check all pods
echo -e "\n=== Checking Pods ==="
kubectl get pods -o wide

# Check pod details and resources
echo -e "\n=== Checking Pod Details ==="
echo "Redis Master:"
kubectl describe pod -l app=redis-master | grep -A 5 "Containers"
echo -e "\nRedis Slave:"
kubectl describe pod -l app=redis-slave | grep -A 5 "Containers"
echo -e "\nFrontend:"
kubectl describe pod -l app=frontend | grep -A 5 "Containers"

# Verify resource requests
echo -e "\n=== Checking Resource Requests ==="
kubectl get pods -l app=redis-master -o jsonpath='{.items[*].spec.containers[*].resources}'
echo
kubectl get pods -l app=redis-slave -o jsonpath='{.items[*].spec.containers[*].resources}'
echo
kubectl get pods -l app=frontend -o jsonpath='{.items[*].spec.containers[*].resources}'

# Check environment variables
echo -e "\n=== Checking Environment Variables ==="
kubectl exec -it $(kubectl get pods -l app=redis-slave -o jsonpath='{.items[0].metadata.name}') -- env | grep GET_HOSTS_FROM
kubectl exec -it $(kubectl get pods -l app=frontend -o jsonpath='{.items[0].metadata.name}') -- env | grep GET_HOSTS_FROM

# Verify ports
echo -e "\n=== Checking Ports ==="
kubectl get service frontend -o jsonpath='{.spec.ports[0]}'
```

## Output

```
thor@jumphost ~$ # Check all deployments
echo "=== Checking Deployments ==="
kubectl get deployments
=== Checking Deployments ===
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
frontend       3/3     3            3           80s
redis-master   1/1     1            1           3m23s
redis-slave    2/2     2            2           2m26s
thor@jumphost ~$ # Check all services
echo -e "\n=== Checking Services ==="
kubectl get services

=== Checking Services ===
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
frontend       NodePort    10.96.231.172   <none>        80:30009/TCP   67s
kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        22m
redis-master   ClusterIP   10.96.201.175   <none>        6379/TCP       3m9s
redis-slave    ClusterIP   10.96.171.152   <none>        6379/TCP       119s
thor@jumphost ~$ # Check all pods
echo -e "\n=== Checking Pods ==="
kubectl get pods -o wide

=== Checking Pods ===
NAME                            READY   STATUS    RESTARTS   AGE     IP            NODE                      NOMINATED NODE   READINESS GATES
frontend-5468469847-fwlpv       1/1     Running   0          101s    10.244.0.8    kodekloud-control-plane   <none>           <none>
frontend-5468469847-jn9xm       1/1     Running   0          101s    10.244.0.9    kodekloud-control-plane   <none>           <none>
frontend-5468469847-v9q45       1/1     Running   0          101s    10.244.0.10   kodekloud-control-plane   <none>           <none>
redis-master-679c6b9b44-764dc   1/1     Running   0          3m44s   10.244.0.5    kodekloud-control-plane   <none>           <none>
redis-slave-67f788df9c-482pf    1/1     Running   0          2m47s   10.244.0.6    kodekloud-control-plane   <none>           <none>
redis-slave-67f788df9c-xx48s    1/1     Running   0          2m47s   10.244.0.7    kodekloud-control-plane   <none>           <none>
thor@jumphost ~$ # Check pod details and resources
echo -e "\n=== Checking Pod Details ==="
echo "Redis Master:"
kubectl describe pod -l app=redis-master | grep -A 5 "Containers"
echo -e "\nRedis Slave:"
kubectl describe pod -l app=redis-slave | grep -A 5 "Containers"
echo -e "\nFrontend:"
kubectl describe pod -l app=frontend | grep -A 5 "Containers"

=== Checking Pod Details ===
Redis Master:
Containers:
  master-redis-xfusion:
    Container ID:   containerd://d8f29181eb418cebada49201e28597425d3d1cbf00d47840e724bcc6980e5328
    Image:          redis
    Image ID:       docker.io/library/redis@sha256:5c7c0445ed86918cb9efb96d95a6bfc03ed2059fe2c5f02b4d74f477ffe47915
    Port:           6379/TCP
--
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-r9lmm:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607

Redis Slave:
Containers:
  slave-redis-xfusion:
    Container ID:   containerd://7660d49f57fb3374d8662a97a1eae412681479544154b76966f3697df238c47e
    Image:          gcr.io/google_samples/gb-redisslave:v3
    Image ID:       gcr.io/google_samples/gb-redisslave@sha256:57730a481f97b3321138161ba2c8c9ca3b32df32ce9180e4029e6940446800ec
    Port:           6379/TCP
--
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-qrm8z:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
--
Containers:
  slave-redis-xfusion:
    Container ID:   containerd://dfef69a095fc53e26abc3360eb5209ce5b0f8c3caacdb9e66385198168e8eeb1
    Image:          gcr.io/google_samples/gb-redisslave:v3
    Image ID:       gcr.io/google_samples/gb-redisslave@sha256:57730a481f97b3321138161ba2c8c9ca3b32df32ce9180e4029e6940446800ec
    Port:           6379/TCP
--
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-86srh:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607

Frontend:
Containers:
  php-redis-xfusion:
    Container ID:   containerd://26146f5a6179bf9f5a048b1cdfbf00c01c2f3b1bee855f779e62730321868c0a
    Image:          gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
    Image ID:       gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
    Port:           80/TCP
--
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-ztzhz:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
--
Containers:
  php-redis-xfusion:
    Container ID:   containerd://de5f096bb05d7b7c3a7e9e36c2fe6d17794c6a5eb628cc8f84df3370994c1ad4
    Image:          gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
    Image ID:       gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
    Port:           80/TCP
--
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-blhcr:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
--
Containers:
  php-redis-xfusion:
    Container ID:   containerd://12ae3c6f2435a948478dee5dae54f5634f0dacb61d5ed21ecf0a5f0ae2a4a665
    Image:          gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
    Image ID:       gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
    Port:           80/TCP
--
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-6cv4z:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
thor@jumphost ~$ # Verify resource requests
echo -e "\n=== Checking Resource Requests ==="
kubectl get pods -l app=redis-master -o jsonpath='{.items[*].spec.containers[*].resources}'
echo
kubectl get pods -l app=redis-slave -o jsonpath='{.items[*].spec.containers[*].resources}'
echo
kubectl get pods -l app=frontend -o jsonpath='{.items[*].spec.containers[*].resources}'

=== Checking Resource Requests ===
{"requests":{"cpu":"100m","memory":"100Mi"}}
{"requests":{"cpu":"100m","memory":"100Mi"}} {"requests":{"cpu":"100m","memory":"100Mi"}}
{"requests":{"cpu":"100m","memory":"100Mi"}} {"requests":{"cpu":"100m","memory":"100Mi"}} {"requests":{"cpu":"100m","memory":"100Mi"}}thor@jumphost ~$ 
thor@jumphost ~$ 
thor@jumphost ~$ # Check environment variables
echo -e "\n=== Checking Environment Variables ==="
kubectl exec -it $(kubectl get pods -l app=redis-slave -o jsonpath='{.items[0].metadata.name}') -- env | grep GET_HOSTS_FROM
kubectl exec -it $(kubectl get pods -l app=frontend -o jsonpath='{.items[0].metadata.name}') -- env | grep GET_HOSTS_FROM

# Verify ports
echo -e "\n=== Checking Ports ==="
kubectl get service frontend -o jsonpath='{.spec.ports[0]}'

=== Checking Environment Variables ===
GET_HOSTS_FROM=dns
GET_HOSTS_FROM=dns

=== Checking Ports ===
{"nodePort":30009,"port":80,"protocol":"TCP","targetPort":80}
```

- Check access the Drupal installation page by clicking on App button.

![alt text](image.png)

***