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
kubectl create deployment clayton-deploy --image=nginx --dry-run=client -o yaml > clayton-deploy.yaml
```
