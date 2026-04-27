# Semea-tec: Ecossistema IoT para Monitoramento Ambiental

Bem-vindo ao repositório do Semea-tec, um projeto de sistema embarcado de ponta a ponta para coleta e visualização de dados ambientais, utilizando ESP32, ESP-IDF, ESP-NOW, LoRa e MQTT.

## Visão Geral do Projeto

O ecossistema é composto por três tipos de dispositivos de hardware e uma stack de backend:

- **Sensor Node**: Coleta dados de sensores (e.g., temperatura, umidade) e os envia via **ESP-NOW**.
- **Bridge Gateway**: Recebe os dados via ESP-NOW e os retransmite para um gateway mestre usando **LoRa**.
- **Master Gateway**: Recebe dados via LoRa e os publica em um broker **MQTT**, conectando-se à internet via Wi-Fi.
- **Backend**: Um servidor em Go que se inscreve no tópico MQTT, processa os dados e os armazena em um banco de dados InfluxDB.

## Navegando no Repositório

A estrutura do projeto foi organizada para manter uma separação clara de responsabilidades:

- **/firmware**: Contém os três projetos de firmware baseados no ESP-IDF.
  - `sensor_node/`: Firmware do nó sensor.
  - `bridge_gateway/`: Firmware do gateway de ponte.
  - `master_gateway/`: Firmware do gateway mestre com Captive Portal.

- **/components**: Contém componentes compartilhados entre os projetos de firmware, como a Camada de Abstração de Hardware (HAL).

- **/server**: Código-fonte do servidor backend em Go.

- **/frontend**: Código-fonte da aplicação de visualização (e.g., React, Vue, Svelte).

- **/docs**: Documentação técnica, guias de contribuição e diagramas de arquitetura.

## Primeiros Passos (Firmware)

1.  **Configure o Ambiente ESP-IDF**: Siga o guia oficial da Espressif para instalar as ferramentas.
2.  **Navegue até um projeto**: `cd firmware/sensor_node`
3.  **Configure o alvo**: `idf.py set-target esp32` (ou `esp32s3`, etc.)
4.  **Compile e Grave**: `idf.py flash monitor`