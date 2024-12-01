# Лабораторная работа №3*: Использование helm chart

## Ход работы

### Установка helm чарта
Helm-чарт — это структура файлов, описывающая Kubernetes-приложение, включая его ресурсы и конфигурации, которые можно развернуть, управлять и обновлять с помощью Helm. Он является шаблонизированным пакетом для Kubernetes, аналогичным пакетным менеджерам вроде APT или Yum для операционных систем.

В корневой директории ```/my-app``` инициализируем helm-чарт

```helm create hello-world```

Автоматически будет создана необходимая структура директорий и файлов.

Сперва отредактируем файл values.yaml. Этот файл задает настройки, которые будут использоваться в шаблонах Helm-чарта для управления приложением

```values.yaml```
<details>
<summary>Показать код</summary>
  
```yaml
# Default values for hello-world.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

# This will set the replicaset count more information can be found here: https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/
replicaCount: 2

image:
  repository: my-app
  pullPolicy: IfNotPresent
  tag: latest

serviceAccount:
  create: true
  name: ""

service:
  type: NodePort
  port: 80
  targetPort: 8080

containerPort: 8080

ingress:
  enabled: false 
  annotations: {}
  hosts:
    - host: chart-example.local
      paths: []
  tls: []

resources: {}

autoscaling:
  enabled: false 
  minReplicas: 1
  maxReplicas: 5
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80

volumes: []

volumeMounts: []

nodeSelector: {}

tolerations: []

affinity: {}
```
</details>

Файл deployment.yaml отвечает за за управление подами и их обновлениями. Он также описывает объект Kubernetes Deployment.

```deployment.yaml```
<details>
<summary>Показать код</summary>
  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-deployment
  labels:
    app: {{ .Release.Name }}-app
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}-new
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}-new
    spec:
      containers:
      - name: {{ .Release.Name }}-container
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        ports:
        - containerPort: {{ .Values.containerPort }}
```
</details>

Файл service.yaml управляет сетевой доступностью приложения и описывает объект Kubernetes Service.

```service.yaml```
<details>
<summary>Показать код</summary>
  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-new-service
spec:
  type: NodePort
  selector:
    app: {{ .Release.Name }}-new
  ports:
    - protocol: TCP
      port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
```
</details>

При развертывании эти файлы используют значения из values.yaml

После применения всех изменений деплоим наш helm-чарт

```helm install my-app ./hello-world```

![image](https://github.com/user-attachments/assets/59cc2729-881a-43e1-b683-cd06836b79d4)

Проверим работоспособность сервиса

```minikube service my-app-new-service --url```

![image](https://github.com/user-attachments/assets/94865ce8-dcc5-4e5d-8e6c-3805ee867860)

![image](https://github.com/user-attachments/assets/d4f1f28c-2219-4c86-b0a6-8c2bbf5d0ab1)



















