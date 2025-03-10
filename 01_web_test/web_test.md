# Kubernetes Deployment: Web-NFS Test

This Kubernetes Deployment configures two Nginx pods that share files via an NFS volume.

- **Replicas**: 2 Nginx containers are deployed.
- **Nginx**: The containers use the `nginx:latest` image and expose port 80.
- **NFS Volume**: The containers mount an NFS volume located at `/mnt/storage/k-data` on the NFS server at `10.0.1.11`. The volume is mounted at `/usr/share/nginx/html` in the container, where Nginx serves files.

This setup allows both Nginx pods to access and serve the same content from the shared NFS storage.

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

# Clean up resources

Delete the following resources using `kubectl`:
```bash
$ k delete svc web-nfs-service
$ kubectl delete deployment web-nfs-test
```