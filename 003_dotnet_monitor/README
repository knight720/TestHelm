[dotnet-monitor](https://learn.microsoft.com/zh-tw/dotnet/core/diagnostics/dotnet-monitor)

```
# deploy
kubectl apply -f deployment.yaml

# query
kubectl describe deployment app

# port forward
kubectl port-forward deployment/app 5000:52323 --pod-running-timeout=10m0s

# query pid (本機 browser 也可以)
curl http://127.0.0.1:5000/processes

# dump (本機 browser 也可以)
curl -o dump.dat http://127.0.0.1:5000/dump?pid=1&type=Full
```