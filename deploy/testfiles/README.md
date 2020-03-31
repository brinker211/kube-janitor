### Procedure to test PVC delete with Kube-janitor

1. `cd kube-janitor`

2. Apply test statefulset & Pod with PVC
```
kubectl apply -f deploy/testfiles/
```

3. Scale down stateful set and confirm that associated pv & pvc still present
```
kubectl scale statefulsets web --replicas=1
kubectl get pv
kubectl get pvc
```

4. Delete sample pod with manual reclaim policy
```
kubectl delete pod task-pv-pod
kubectl get pv
kubectl get pvc
```

5. Deploy Kube-janitor
```
kubectl apply -f deploy/common/
kubectl apply -f deploy/deployments/
```

6. Check kube-janitor pod logs to confirm the delete
```
kubectl logs kube-janitor-<>

# Output that shows test:

Rule delete-pvc with JMESPath "_context.pvc_is_not_mounted && _context.pvc_is_not_referenced" evaluated for PersistentVolumeClaim default/task-pv-claim: True
Deleting PersistentVolumeClaim default/task-pv-claim..

Rule delete-pvc with JMESPath "_context.pvc_is_not_mounted && _context.pvc_is_not_referenced" evaluated for PersistentVolumeClaim default/www-web-0: False
PersistentVolumeClaim default/www-web-1 is referenced by StatefulSet web
```
