# Репозиторий для выполнения домашних заданий курса "Инфраструктурная платформа на основе Kubernetes-2024-02" 
# Переходим в каталог куда склонированы файлы с репозитория (у меня /opt/andrewtsvirko_repo)
# Создаем namespace homework

#Добавляем метку на node minikube чтобы в последующем применилось affinity и поды расположились только на нодах с меткой homework=true
kubectl label nodes minikube homework=true

#Установим ингресс контроллер в миникуб
minikube addons enable ingress

Новая проба по ДЗ
Было:
 readinessProbe:
            exec:
              command:
                - cat
                - /homework/index.html
            initialDelaySeconds: 5
            periodSeconds: 5
Стало:
readinessProbe:
            httpGet:
              path: /index.html
              port: 8000
            initialDelaySeconds: 5
            periodSeconds: 5

Заменим тип сервиса с прошлой ДЗ
Было:
apiVersion: v1
kind: Service
metadata:
  name: web-server-service
  namespace: homework
spec:
  selector:
    app: web-server
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
  type: NodePort
 
 Стало:

apiVersion: v1
kind: Service
metadata:
  name: web-server-service
  namespace: homework
spec:
  selector:
    app: web-server
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
  type: ClusterIP

Более подробное описание кластера было в предыдущей ветке, буду писать более кратко.
Находясь в каталоге проекта выполнить применить следующие манифесты:
kubectl apply -f namespace.yaml 
kubectl apply -f nginx-configmap.yaml
kubectl apply -f web-service.yaml
kubectl apply -f deployment.yaml
kubectl apply -f ingress.yaml

Результат выполнения и проверки:
![Screenshot 2024-05-26 122053](https://github.com/Kuber-2024-04OTUS/andrewtsvirko_repo/assets/83001395/35709ce0-81e8-4c2c-b443-061d56b08db4)

Таким образом задание считаю выполненным

