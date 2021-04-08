# dolgosheev_platform

В первую очередь, необходимо собрать Dockerfile (присваиваю сразу имя репа, чтобы потом не тегать)

```text
docker build -t v0lshebnick/web-server:latest kubernetes-intro/web
```

пушим в реп

```text
docker push v0lshebnick/web-server:latest
```

запускаем под с манифестом

```text
kubectl.exe apply -f web-pod.yaml
```

наблюдаем за процессом запуска

```text
kubectl get pods -w
```

форвардим на локалхост

```text
kubectl port-forward --address 0.0.0.0 pod/web 8000:8000
```

чекаем из браузера по ссылке 

```text
http://127.0.0.1:8000/
```