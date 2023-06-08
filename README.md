# IoT
![image](https://github.com/ignorira/IoT/assets/58992611/daaf0f56-a876-4f14-9ccf-8b6896ed82cc)
Home Assistant — сердце умного дома
автоматизация. Он также содержит следующие компоненты:

+ InfluxDB
+ Grafana
+ MQTT (Mosquitto)

## Docker-Compose
Используется файл docker-compose.yml для сохранения конфигурации
```
version: '3.3'
services:

    homeassistant:
        container_name: homeassistant
        image: ghcr.io/home-assistant/home-assistant:stable
        depends_on:
            - mosquitto
            - influxdb
        volumes:
            - ./config:/config
            - /etc/localtime:/etc/localtime:ro
        ports:
            - "8123:8123"
        restart: always
        networks:
            - mosquitto

    influxdb:
        image: influxdb:2.0.7
        environment:
            DOCKER_INFLUXDB_INIT_MODE: setup
            DOCKER_INFLUXDB_INIT_USERNAME: ${INFLUXDB_USERNAME}
            DOCKER_INFLUXDB_INIT_PASSWORD: ${INFLUXDB_PASSWORD}
            DOCKER_INFLUXDB_INIT_ORG: ${INFLUXDB_ORG}
            DOCKER_INFLUXDB_INIT_BUCKET: ${INFLUXDB_BUCKET}
            DOCKER_INFLUXDB_INIT_ADMIN_TOKEN: ${INFLUXDB_TOKEN}
        ports:
            - "8086:8086"
        networks:
            - mosquitto

    grafana:
        image: grafana/grafana-enterprise:8.2.0
        restart: unless-stopped
        depends_on:
            - influxdb
        volumes:
            - ./grafana:/var/lib/grafana
            - ./grafana/provisioning/:/etc/grafana/provisioning/
      
        ports:
            - "3000:3000"
        networks:
            - mosquitto

    mosquitto:
        image: eclipse-mosquitto:2
        ports:
            - "1883:1883"
        volumes:
            - C:\Users\79819\mosqtest\config:/mosquitto/config
            - C:\Users\79819\mosqtest\data:/mosquitto/data
            - C:\Users\79819\mosqtest\log:/mosquitto/log
        networks:
            - mosquitto
networks:
    mosquitto:
        name: mosquitto
        driver: bridge
```
## InfluxDB
InfluxDB — это база данных временных рядов с открытым исходным кодом, которая используется для хранения всех изменений состояния из Home Assistant и настройки системы мониторинга данных.

## Grafana
Grafana используется для визуализации данных.

## MQTT (Mosquitto)
MQTT (Message Queuing Telemetry Transport) — это протокол публикации/подписки сообщений, предназначенный для работы с ограниченной пропускной способностью и небольшим объемом кода. Алгоритм работы системы следующий. Клиенты подключаются к серверу и сразу после подключения каждый из них осуществляет подписку на и интересующие его топики. Топики – это механизм, позволяющий называть MQTT сущности в понятном для человека виде. Всё общение между клиентами проходит транзитом через сервер, который перенаправляет данные другим клиентам с учетом их подписок
