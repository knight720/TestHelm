# dotnet-monitor
測試 dotnet-monitor 在 k8s 上是否可以 dump
## Note
- 241216  
在 dotnet 8 使用 dotnet-mointor 6 錯誤訊息如下  
deployment 的 spec.template.metadata.annotations 加上 monitoring.91app.io/inject-dotnet-monitor: 'true'
```
System.NullReferenceException: Object reference not set to an instance of an object.
    at Microsoft.Diagnostics.Monitoring.WebApi.PrometheusDataModel.GetPrometheusNormalizedName(String metricProvider, String metric, String unit)
    at Microsoft.Diagnostics.Monitoring.WebApi.MetricsStore.SnapshotMetrics(Stream outputStream, CancellationToken token)
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.MetricsController.\u003CGetMetrics\u003Eb__5_1(Stream outputStream, CancellationToken token)
    at Microsoft.Diagnostics.Monitoring.WebApi.OutputStreamResult.\u003C\u003Ec__DisplayClass6_0.\u003C\u003CExecuteResultAsync\u003Eb__0\u003Ed.MoveNext() --- End of stack trace from previous location ---
    at Microsoft.Diagnostics.Monitoring.WebApi.ActionContextExtensions.\u003C\u003Ec__DisplayClass1_0.\u003C\u003CInvokeAsync\u003Eb__0\u003Ed.MoveNext() --- End of stack trace from previous location ---
    at Microsoft.Diagnostics.Monitoring.WebApi.ExecutionHelper.InvokeAsync[T](Func\u00602 action, ILogger logger, CancellationToken token)
    at Microsoft.Diagnostics.Monitoring.WebApi.ActionContextExtensions.InvokeAsync(ActionContext context, Func\u00602 action, ILogger logger)
    at Microsoft.Diagnostics.Monitoring.WebApi.OutputStreamResult.ExecuteResultAsync(ActionContext context)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeNextResultFilterAsync\u003Eg__Awaited|30_0[TFilter,TFilterAsync](ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.Rethrow(ResultExecutedContextSealed context)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.ResultNext[TFilter,TFilterAsync](State\u0026 next, Scope\u0026 scope, Object\u0026 state, Boolean\u0026 isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.InvokeResultFilters() --- End of stack trace from previous location ---
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeFilterPipelineAsync\u003Eg__Awaited|20_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeAsync\u003Eg__Awaited|17_0(ResourceInvoker invoker, Task task, IDisposable scope)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeAsync\u003Eg__Awaited|17_0(ResourceInvoker invoker, Task task, IDisposable scope)
    at Microsoft.AspNetCore.Routing.EndpointMiddleware.\u003CInvoke\u003Eg__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
    at Microsoft.Diagnostics.Tools.Monitor.RequestLimitMiddleware.Invoke(HttpContext context)
    at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
    at Microsoft.AspNetCore.Authentication.AuthenticationMiddleware.Invoke(HttpContext context)
    at Microsoft.AspNetCore.Server.Kestrel.Core.Internal.Http.HttpProtocol.ProcessRequests[TContext](IHttpApplication\u00601 application)
```
- 241212  
使用 deployment_6.yaml 有重現無法 dump 的問題
```
System.UnauthorizedAccessException: Access to the path \u0027/diag/dumps/a4f0b391-6350-4f17-b39e-03da414f11cd_1\u0027 is denied.  ---\u003E System.IO.IOException: Permission denied    --- End of inner exception stack trace ---
    at Interop.ThrowExceptionForIoErrno(ErrorInfo errorInfo, String path, Boolean isDirectory, Func\u00602 errorRewriter)
    at Microsoft.Win32.SafeHandles.SafeFileHandle.Open(String path, OpenFlags flags, Int32 mode)
    at Microsoft.Win32.SafeHandles.SafeFileHandle.Open(String fullPath, FileMode mode, FileAccess access, FileShare share, FileOptions options, Int64 preallocationSize)
    at System.IO.Strategies.OSFileStreamStrategy..ctor(String path, FileMode mode, FileAccess access, FileShare share, FileOptions options, Int64 preallocationSize)
    at System.IO.Strategies.FileStreamHelpers.ChooseStrategy(FileStream fileStream, String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize, FileOptions options, Int64 preallocationSize)
    at System.IO.FileStream..ctor(String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize, FileOptions options)
    at Microsoft.Diagnostics.Monitoring.WebApi.DumpService.AutoDeleteFileStream..ctor(String path)
    at Microsoft.Diagnostics.Monitoring.WebApi.DumpService.DumpAsync(IEndpointInfo endpointInfo, DumpType mode, CancellationToken token)
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.DiagController.\u003C\u003Ec__DisplayClass17_0.\u003C\u003CCaptureDump\u003Eb__0\u003Ed.MoveNext() --- End of stack trace from previous location ---
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.DiagController.\u003C\u003Ec__DisplayClass35_0.\u003C\u003CInvokeForProcess\u003Eb__0\u003Ed.MoveNext() --- End of stack trace from previous location ---
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.DiagController.\u003C\u003Ec__DisplayClass37_0\u00601.\u003C\u003CInvokeForProcess\u003Eb__0\u003Ed.MoveNext() --- End of stack trace from previous location ---
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.DiagControllerExtensions.InvokeService[T](ControllerBase controller, Func\u00601 serviceCall, ILogger logger)
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.DiagController.InvokeForProcess[T](Func\u00602 func, Nullable\u00601 processKey, String artifactType)
    at Microsoft.Diagnostics.Monitoring.WebApi.Controllers.DiagController.InvokeForProcess(Func\u00602 func, Nullable\u00601 processKey, String artifactType)
    at lambda_method13(Closure , Object )
    at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.TaskOfActionResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.\u003CInvokeActionMethodAsync\u003Eg__Awaited|12_0(ControllerActionInvoker invoker, ValueTask\u00601 actionResultValueTask)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.\u003CInvokeNextActionFilterAsync\u003Eg__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State\u0026 next, Scope\u0026 scope, Object\u0026 state, Boolean\u0026 isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.\u003CInvokeInnerFilterAsync\u003Eg__Awaited|13_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeNextExceptionFilterAsync\u003Eg__Awaited|26_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.Rethrow(ExceptionContextSealed context)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.Next(State\u0026 next, Scope\u0026 scope, Object\u0026 state, Boolean\u0026 isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeFilterPipelineAsync\u003Eg__Awaited|20_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeAsync\u003Eg__Awaited|17_0(ResourceInvoker invoker, Task task, IDisposable scope)
    at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.\u003CInvokeAsync\u003Eg__Awaited|17_0(ResourceInvoker invoker, Task task, IDisposable scope)
    at Microsoft.AspNetCore.Routing.EndpointMiddleware.\u003CInvoke\u003Eg__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
    at Microsoft.Diagnostics.Tools.Monitor.RequestLimitMiddleware.Invoke(HttpContext context)
    at Microsoft.AspNetCore.Authorization.Policy.AuthorizationMiddlewareResultHandler.HandleAsync(RequestDelegate next, HttpContext context, AuthorizationPolicy policy, PolicyAuthorizationResult authorizeResult)
    at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
    at Microsoft.AspNetCore.Authentication.AuthenticationMiddleware.Invoke(HttpContext context)
    at Microsoft.AspNetCore.Server.Kestrel.Core.Internal.Http.HttpProtocol.ProcessRequests[TContext](IHttpApplication\u00601 application)
```
## Command
- docker compose
```
docker compose up
```
- K8s
```
# deploy
kubectl apply -f deployment.yaml

# check deployment
kubectl get deployment

# query
kubectl describe deployment app

# port forward
kubectl port-forward deployment/app 5000:52323 --pod-running-timeout=10m0s

# query pid (本機 browser 也可以直接開啟)
curl http://127.0.0.1:5000/processes

# dump (本機 browser 也可以直接開啟)
curl -o dump.dat http://127.0.0.1:5000/dump?pid=1&type=Full

# 查詢 logs
kubectl logs -c monitor deploy-exampleapp-7798fb4547-964x6
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
[Security Context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)