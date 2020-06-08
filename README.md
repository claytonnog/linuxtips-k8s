# Readme

### em construção 08-06-2020

Para criar um arquivo yaml de exemplo a partir de uma imagem:
```
$ kubectl run --image=nginx clayton_nginx --dry-run=client -o yaml > clayton_nginx.yaml
```
sendo:
- clayton_nginx o nome do pod
- clayton_nginx.yaml a saida

Ao rodar o kubectl create veremos um erro relacionado ao nome do pod, por causa do "_"... é apenas alterar para "-":

```
$ kubectl create -f clayton_nginx.yaml
```

Para criar um arquivo de teste de deploy faça:
```
$ kubectl create deployment clayton-deploy --image=nginx --dry-run=client -o yaml > clayton-deploy.yaml
$ kubectl create -f clayton-deploy.yaml
```
Para apagar o deployment criado:
```
$ kubectl delete deployments.apps clayton-deploy
```

Criar um namespace:
```
$ kubectl create namespace clayton-namespace
```
Criar um deployment dentro do namespace criado:
```
$ kubectl create -f clayton-deploy.yaml -n clayton-namespace
$ kubectl get deploy -n clayton-namespace
$ kubectl get pods -n clayton-namespace
```

Rodar o container nginx e expor a porta 80 (criando assim um serviço - service)
``` 
$ kubectl run --image=nginx nginx
pod/nginx created
$ kubectl expose pod nginx --port=80
service/nginx exposed
$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          40s
$ kubectl get services
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   99m
nginx        ClusterIP   10.103.148.170   <none>        80/TCP    16s
``` 

Deletando o service criado e criado outro expondo a porta externamente:
```
$ kubectl delete service nginx
service "nginx" deleted
$ kubectl expose pod nginx --port=80 --type=NodePort
service/nginx exposed
$ kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        102m
nginx        NodePort    10.104.65.54   <none>        80:30588/TCP   5s
```

Pegue o ip do minikube e depois teste no browser ou via curl:
```
$ minikube ip
$ curl http://192.168.99.101:30588/
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

**OBS: Usar o service-loadbalancer e o service-nodeport em conjunto com o clayton_nginx.**


Criação de deployment:
```
$ kubectl create deployment --image=nginx nginx
deployment.apps/nginx created
$ kubectl get deployments.apps nginx -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES   SELECTOR
nginx   1/1     1            1           38s   nginx        nginx    app=nginx
$ kubectl get pods nginx-f89759699-nhr8w -o wide
NAME                    READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx-f89759699-nhr8w   1/1     Running   0          72s   172.17.0.6   minikube   <none>           <none>
```

### Scale:
```
$ kubectl create deployment --image=nginx nginx
deployment.apps/nginx created
$ kubectl get deployments.apps nginx -o wide
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES   SELECTOR
nginx   1/1     1            1           38s   nginx        nginx    app=nginx
$ kubectl get pods nginx-f89759699-nhr8w -o wide
NAME                    READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx-f89759699-nhr8w   1/1     Running   0          72s   172.17.0.6   minikube   <none>           <none>
```

### Limitar recursos:

Verificar o arquivo deploy-limiteado.yaml
**Atenção! 1 core de CPU corresponde a 1000m (1000 milicore). Ao especificar 200m, estamos querendo reservar 20% de 1 core da CPU. Se fosse informado o valor 0.2 teria o mesmo efeito, ou seja, seria reservado 20% de 1 core da CPU.**

### Limitando recursos por namespace
Criando o namespace:
```
$ kubectl create namespace namespace111
```

Aplicando o limite (limitando-recursos.yaml) ao namespace criado anteriormente:
```
$ kubectl create -f limitando-recursos.yaml -n namespace111
```

Verificando:
```
$ kubectl describe namespaces namespace111 
Name:         namespace111
Labels:       <none>
Annotations:  <none>
Status:       Active

No resource quota.

Resource Limits
 Type       Resource  Min  Max  Default Request  Default Limit  Max Limit/Request Ratio
 ----       --------  ---  ---  ---------------  -------------  -----------------------
 Container  cpu       -    -    500m             1              -
 Container  memory    -    -    80Mi             100Mi          -

```
```
$ kubectl get limitrange -n namespace111
```


