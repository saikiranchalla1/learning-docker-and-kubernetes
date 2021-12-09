```
kubectl create -f helloworld.yml
```

```
kubectl get deployments
```

```
kubectl get rs
```

```
kubectl get pods
```

```
kubectl get pods --show-labels
```

```
kubectl rollout status deployment/helloworld-deployment
```

```
kubectl expose deployment helloworld-deployment --type=NodePort
```

```
kubectl get svc
```

```
kubectl set image deployment/helloworld-deployment k8s-demo=wardviaene/k8s-demo:2
```

```
kubectl rollout history deployment/helloworld-deployment
```

Change cause is NONE. This can be set using `--record` option.

```
kubectl rollout undo deployment/helloworld-deployment
```

By default, only 2 revisions are stored. To increase this limit, set the `rolloutHistoryLimit` in the definition file to a required value.

```
kubectl rollout undo deployment/helloworld-deployment --to-revision=2
```

