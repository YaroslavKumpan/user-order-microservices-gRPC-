# Используем образ Python 3.11 slim
FROM python:3.11-slim

# Устанавливаем рабочую директорию
WORKDIR /order_service

# Копируем файлы для установки зависимостей
COPY pyproject.toml poetry.lock ./

# Устанавливаем Poetry и зависимости
RUN pip install --upgrade pip && \
    pip install poetry && \
    poetry config virtualenvs.create false

# Создаем пустые файлы в директории app для предотвращения ошибки Poetry
RUN mkdir -p app && \
    touch app/__init__.py && \
    touch app/grpc_server.py

# Устанавливаем зависимости проекта
RUN poetry install --no-dev

# Копируем все остальные файлы проекта
COPY . .

# Генерируем Python-код из proto-файлов
RUN mkdir -p generated && \
    python -m grpc_tools.protoc --python_out=generated --grpc_python_out=generated --pyi_out=generated --proto_path=./proto ./proto/*.proto

# Устанавливаем PYTHONPATH, чтобы найти сгенерированные файлы
ENV PYTHONPATH="${PYTHONPATH}:/order_service/generated"

# Указываем команду для выполнения при запуске контейнера
CMD ["poetry", "run", "python", "app/grpc_server.py"]