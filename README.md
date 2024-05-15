# Репозиторий для выполнения домашних заданий курса "Инфраструктурная платформа на основе Kubernetes-2024-02" 
# Переходим в каталог куда склонированы файлы с репозитория (у меня /opt/andrewtsvirko_repo)
# Создаем namespace homework
kubectl apply -f namespace.yaml
#namespace/homework created


#Чтобы не указывать в каждой команде -n homework:
kubectl config set-context --current --namespace=homework
#Context "minikube" modified.

#Добавляем метку на node minikube чтобы в последующем применилось affinity и поды расположились только на нодах с меткой homework=true
kubectl label nodes minikube homework=true

#Применяем манифест configmap для корректной работы nginx (требуется для проверки работоспособности пода)
kubectl apply -f nginx-configmap.yaml
#root@minukube:/opt/andrewtsvirko_repo# kubectl get configmap
#NAME               DATA   AGE
#kube-root-ca.crt   1      68s
#nginx-config       1      14s


#Создаем сервис для доступности по сети
kubectl apply -f web-service.yaml
#service/web-server-service created
#root@minukube:/opt/andrewtsvirko_repo# kubectl get services
#NAME                 TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
#web-server-service   NodePort   10.97.138.116   <none>        80:31729/TCP   8s

#Применяем манифест deployment 
kubectl apply -f deployment.yaml
#deployment.apps/firstpod-deployment created

#Проверим наличие репликисет
root@minukube:/opt/andrewtsvirko_repo# kubectl get rs
#NAME                             DESIRED   CURRENT   READY   AGE
#firstpod-deployment-6f74c65ddb   3         3         3       22s

#Проверим наличие объектов деплоймент
root@minukube:/opt/andrewtsvirko_repo# kubectl get deploy
#NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
#firstpod-deployment   3/3     3            3           68s

#Посмотрим запустились ли поды
root@minukube:/opt/andrewtsvirko_repo# kubectl get pods
#NAME                                   READY   STATUS    RESTARTS   AGE
#firstpod-deployment-6f74c65ddb-5q6zf   1/1     Running   0          108s
#firstpod-deployment-6f74c65ddb-9nj9s   1/1     Running   0          108s
#firstpod-deployment-6f74c65ddb-x8fhc   1/1     Running   0          108s

#Не знаю других способов проверки работоспособности пода с nginx кроме как запросить curl
#Узнаем адрес сервиса
minikube service web-server-service --url -n homework
#http://192.168.49.2:31729

#Провалимся внутрь minukube (поскольку настраивать доступ извне пока не научились)
minikube ssh
#docker@minikube:~$

#Запросим curl по адресу http://192.168.49.2:31729
docker@minikube:~$ curl 192.168.49.2:31729
#Hello World

![image](https://github.com/Kuber-2024-04OTUS/andrewtsvirko_repo/assets/83001395/29b7204c-c4b9-42e3-9b24-04b0d95c0bc7)

#Таким образом задание считаю выполненным, поды работают корректно, задача со * выполнена
