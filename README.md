# dolgosheev_platform

<details>
<summary>kubernetes-intro</summary>

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

</details>

<details>
<summary>kubernetes-controllers</summary>

В первую очередь, необходимо собрать Dockerfile фронтедна хипстершопа ( из папки с ним )

```text
docker build -t v0lshebnick/frontend:v0.0.1 .
docker build -t v0lshebnick/frontend:v0.0.2 .
```

пушим в реп

```text
docker push v0lshebnick/frontend:v0.0.1
docker push v0lshebnick/frontend:v0.0.2
```

Далее применяем rs - frontend-replicaset.yaml, убеждаемся что реплика поднялась 1 подом

```text
kubectl.exe apply -f frontend-replicaset.yaml
```

Увеличиваем до 3 подов

```text
kubectl scale replicaset frontend --replicas=3
```

Проверяем, что удалив поды они пересоздадутся

```text
kubectl delete pods -l app=frontend | kubectl get pods -l app=frontend -w
```

Проделываем те же самые операции для <b>paymentservice</b>

```text
v0lshebnick/paymentservice:v0.0.1
v0lshebnick/paymentservice:v0.0.2
```

Собираем для него deployment и применяем его paymentservice-deployment.yaml

```text
kubectl.exe apply -f paymentservice-deployment.yaml
```
</details>