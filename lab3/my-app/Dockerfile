# Dockerfile
FROM python:3.9-slim

WORKDIR /my-app

# Копируем наш код
COPY server.py /my-app

# Открываем порт
EXPOSE 8080

# Запускаем сервер
CMD ["python", "server.py"]