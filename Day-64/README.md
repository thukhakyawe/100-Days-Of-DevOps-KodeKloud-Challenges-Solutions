# Step 1: Check Basic Resource Status

# 1. Check if deployment exists and its status
```
kubectl get deployment python-deployment-nautilus
```

# 2. Check if service exists
```
kubectl get service python-deployment-nautilus
```


# 3. Check all pods and their status
```
kubectl get pods
```

![alt text](image.png)

# Step 2: Check Pod Details

# 4. Check detailed pod status
```
kubectl get pods -o wide
```

# 5. Check pod labels
```
kubectl get pods --show-labels | grep python
```

![alt text](image-1.png)

# Step 3: Investigate Deployment Configuration

# 6. Check what image the deployment is using
```
kubectl get deployment python-deployment-nautilus -o jsonpath='{.spec.template.spec.containers[0].image}' && echo
```

# 7. Check container port configuration
```
kubectl get deployment python-deployment-nautilus -o jsonpath='{.spec.template.spec.containers[0].ports}' && echo
```

![alt text](image-2.png)

# Step 4: Check for Errors

# 8. Check deployment events for errors
```
kubectl describe deployment python-deployment-nautilus
```

![alt text](image-3.png)

# 9. Check the failing pod details
```
kubectl describe pod python-deployment-nautilus-94c6999b7-7985h 
```

![alt text](image-4.png)

# 10. Check pod logs
```
kubectl logs python-deployment-nautilus-94c6999b7-7985h 
```

![alt text](image-5.png)


# Fix the image name to the correct one
```
kubectl set image deployment/python-deployment-nautilus python-container-nautilus=poroko/flask-demo-app:latest
```

![alt text](image-6.png)

# Wait for the new pod to be ready
```
kubectl rollout status deployment/python-deployment-nautilus --timeout=120s
```

![alt text](image-7.png)

# Create NodePort service
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: python-service-nautilus
spec:
  type: NodePort
  selector:
    app: python_app
  ports:
  - port: 80
    targetPort: 5000
    nodePort: 32345
EOF
```

![alt text](image-12.png)

# Check what port was assigned
```
kubectl get service python-service-nautilus 
```

![alt text](image-9.png)



# Check all resources
```
kubectl get deployments.apps 
kubectl get pod python-deployment-nautilus-5df497bffb-dnsnm 
kubectl get service python-service-nautilus 
```
![alt text](image-8.png)


# Test the application
```
NODE_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[0].address}')
SERVICE_PORT=$(kubectl get service python-service-nautilus  -o jsonpath='{.spec.ports[0].nodePort}')
echo "Test application at: http://$NODE_IP:$SERVICE_PORT"
curl -s http://$NODE_IP:$SERVICE_PORT
```

![alt text](image-10.png)