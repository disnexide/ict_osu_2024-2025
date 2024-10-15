# Лабораторная работа №3: Поднятие кластера Kubernetes

## Ход работы

### Создание директории приложения
В качестве [сервиса](https://github.com/disnexide/ict_osu_2024-2025/blob/main/lab3/my-app/server.py) был написан простой сервер на языке Python, который выводит в браузере "Hello World!". Также, для создания локального образа был прописан [Dockerfile](https://github.com/disnexide/ict_osu_2024-2025/blob/main/lab3/my-app/Dockerfile).

### Сборка образа
В директории my-app собираем образ командой docker build -t my-app .

![1 (2)](https://github.com/user-attachments/assets/aff95252-0926-41f0-afa5-46844e038d4c)

### Установка и запуск Minikube
Устанавливаем и запускаем Minikube, загружаем наш образ, проверяем командой minikube image ls

![2](https://github.com/user-attachments/assets/87c9736b-8a45-4d37-adbe-68ac6a9dac80)

### Определение ресурсов Kubernetes 
Для управления приложением было создано два yaml-файла: deploy-my-app.yaml и service.yaml

![3](https://github.com/user-attachments/assets/82466e70-2be6-4784-87b2-410d13ce5d4b)

**Deployment** — это ресурс Kubernetes, который управляет развертыванием и поддержанием желаемого состояния приложения в виде подов (контейнеров).

- **apiVersion:**
Указываем версию API Kubernetes
- **kind:**
Тип ресурса — Deployment, отвечает за управление подами
- **metadata:**
Метаданные, например, имя деплоймента
- **replicas:**
Количество подов для деплоймента
- **selector:**
Селектор, который связывает деплоймент с подами по метке
- **template:**
Шаблон пода, включает в себя метки и описание контейнеров (имя, образ, порт, на котором приложение будет доступно внутри контейнера)

![4](https://github.com/user-attachments/assets/f3430c0b-e788-49f0-af75-a9cec640ccbb)

**Service** — это ресурс, который определяет, как другие приложения или внешние клиенты могут получить доступ к подам.

- **ports:**
Описание портов (протокол, внешний порт и внутренний порт контейнера)
- **type:**
Тип сервиса. NodePort позволяет получить доступ к приложению через выделенный порт

### Деплоймент сервиса
Применение созданных ресурсов было выполнено командой kubectl apply -f deploy-my-app.yaml -f service.yaml

![5](https://github.com/user-attachments/assets/f35c6274-4784-4f5b-a1cf-56a24ef1ae20)

### Проверка работоспособности
Чтобы открыть сервис в браузере была выполнена команда minikube service my-app-service

![6](https://github.com/user-attachments/assets/1111bc88-bb7a-48b4-872a-cdd9d0b750b9)


