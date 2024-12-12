測試 dotnet-monitor 在 k8s 上是否可以 dump
## Command
- docker compose
```
docker compose up
```
- K8s
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
- dotnet monitor
```
# get pid
http://127.0.0.1:52323/processes

# dump
http://127.0.0.1:52323/dump?pid=1

# dump
http://127.0.0.1:52323/dump?pid=1&type=Full
```
## Reference
- dotnet-monitor  
[MSDN](https://learn.microsoft.com/zh-tw/dotnet/core/diagnostics/dotnet-monitor)  
[GitHub](https://github.com/dotnet/dotnet-monitor/blob/main/documentation/README.md)  
[Running in Docker Compose](https://github.com/dotnet/dotnet-monitor/blob/main/documentation/docker-compose.md)  
[Running in Kubernetes](https://github.com/dotnet/dotnet-monitor/blob/main/documentation/kubernetes.md)