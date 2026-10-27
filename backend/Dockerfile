"Мы	выбираем	легковесный	образ	Python	3.12 как	базовый.	
FROM python:3.12-slim AS builder

"Устанавливаем	/build как	рабочую	директорию."
WORKDIR /build

"Копируем	файл	requirements.txt	из	локальной	файловой	системы	в
образ."
COPY requirements.txt .

"Устанавливаем	зависимости	из	requirements.txt.	Флаг	--no-cache-dir
предотвращает	кэширование	установленных	пакетов,	что	позволяет
уменьшить	размер	образа"
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt


FROM python:3.12-slim

"Создаём непривилегированного пользователя"
RUN useradd --create-home --shell /bin/bash appuser

"Устанавливаем	/app как	рабочую	директорию."
WORKDIR /app

# Копируем только установленные пакеты из первого этапа
COPY --from=builder /install /usr/local

# Копируем код приложения
COPY main.py .

#Создаём папку для статики и передаём её appuser
RUN mkdir -p /app/static && chown -R appuser:appuser /app

# Переключаемся на непривилегированного пользователя
USER appuser

#Указываем,	что	приложение	будет	слушать	на	порту	8080 
EXPOSE 8080

#Определяем	команду,	которая	будет	выполнена	при	запуске	контейнера
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]