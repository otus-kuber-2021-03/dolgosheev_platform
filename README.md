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

<details>
<summary>kubernetes-security</summary>

<h3>Task1</h3>

```text
01_service_account_bob.yaml - создаём сервисный акк bob

02_clusterrole_bob.yaml - даём bob права админа кластера

03_service_account_dave.yaml - создаём сервисный акк dave
```

<h3>Task2</h3>

```text
01_namespace.yaml - создаём ns prometheus

02_service_account_carol.yaml - создаем sa carol

03_cluster_role.yaml - выдаем всем sa определенные права

04_clustre_role_binding.yaml - выставляем права
```

<h3>Task3</h3>

```text
01_namespace_dev.yaml - создаём ns dev

02_service_account_jane.yaml - создаем sa jane в ns dev

03_role_binding_jane.yaml - выдаем sa jane role admin в ns dev

04_service_account_ken.yaml - создаём ns ken

05_role_binding_ken.yaml - выдаем sa ken role view в ns dev
```
</details>

<details>
<summary>kubernetes-volumes</summary>

<h3>Развернем StatefulSel c local S3 (min.io) </h3>

```text
minio-statefulset.yaml - манифест
```

применим его

```text
kubectl apply -f minio-statefulset.yaml
```

для его доступности изнустри кластера создадим Headless Service

```text
minio-headless-service.yaml - манифест
```

применим его

```text
kubectl apply -f minio-headless-service.yaml
```

в целях безопасности поместим конфиденциальные данные в secrets, для этого 

создадим 2 файлика с секретными данными

```text
echo -n 'minio' > ./access.txt
echo -n 'minio123' > ./secret.txt
```

далее создадим секрет из данных в этих файлах

```text
kubectl create secret generic db-user-pass \
  --from-file=./access.txt \
  --from-file=./secret.txt
```

проверим что запись появилась 

```text
kubectl get secrets
.
.
.
$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
db-user-pass          Opaque                                2      8s
```

можем проверить запись в виде base64

```text
kubectl describe secrets/db-user-pass
.
.
.
{"password.txt":"bWluaW8xMjM=","username.txt":"bWluaW8="}
```

можем дефишровать и посмотреть настоящие данные

```text
echo 'bWluaW8xMjM=' | base64 --decode
.
.
.
minio123
```

далее просто добавим их в манифест стейтфулсета в виде values, пересоздадим кластер
</details>