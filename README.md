# «Сетевое взаимодействие в K8S. Часть 1» - Дрибноход Давид

### Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

1. Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.

#### Ответ:

Создал namespase ```kubectl create namespace 12-04-hw```

Создал файл deployment.yaml, применил его и проверил
``` YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: network-tools
  namespace: 12-04-hw
spec:
  selector:
    matchLabels:
      app: nettools
  replicas: 3
  template:
    metadata:
      labels:
        app: nettools
    spec:
      containers:
      - name: nginx
        image: nginx:1.25.4
        ports:
        - containerPort: 80
      - name: multitool
        image: wbitt/network-multitool
        ports:
        - containerPort: 8080
        env:
          - name: HTTP_PORT
            value: "8088"
```
![image](https://github.com/DrDavidN/12-04-hw/assets/128225763/f119cdef-bd4b-4c6c-9939-588b393a3f94)

2. Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.

#### Ответ:

Создаю файл service.yaml, применяю его и проверяю
``` YAML
apiVersion: v1
kind: Service
metadata:
  name: network-service
  namespace: 12-04-hw
spec:
  selector:
    app: nettools
  ports:
    - protocol: TCP
      name: nginx
      port: 9001
      targetPort: 80
    - protocol: TCP
      name: multitool
      port: 9002
      targetPort: 8088
```
![image](https://github.com/DrDavidN/12-04-hw/assets/128225763/58b8347a-814a-45d7-9951-a6fd2cf6a108)

3. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.

#### Ответ:

Создаю файл multitool.yaml, применяю его, проверяю
``` YAML
apiVersion: v1
kind: Pod
metadata:
   name: multitool
   namespace: 12-04-hw
spec:
   containers:
     - name: multitool
       image: wbitt/network-multitool
       ports:
        - containerPort: 8080
```


4. Продемонстрировать доступ с помощью `curl` по доменному имени сервиса.

#### Ответ:

5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

#### Ответ:

------

### Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

1. Создать отдельный Service приложения из Задания 1 с возможностью доступа снаружи кластера к nginx, используя тип NodePort.

#### Ответ:

2. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.

#### Ответ:

3. Предоставить манифест и Service в решении, а также скриншоты или вывод команды п.2.

#### Ответ:

------
