# Arquitetura do Sistema Semea-tec

**Versão: 1.0**

Este documento descreve a arquitetura de alto nível do ecossistema Semea-tec, o fluxo de dados e as principais decisões de design.

## 1. Visão Geral

O Semea-tec é um sistema de monitoramento ambiental distribuído, projetado para ser de baixo custo, baixo consumo de energia e escalável. Ele é composto por três camadas de hardware e uma camada de software (backend/frontend).

- **Camada 1 (Coleta)**: `Sensor Node`
- **Camada 2 (Agregação/Repasse)**: `Bridge Gateway`
- **Camada 3 (Conectividade Cloud)**: `Master Gateway`
- **Camada 4 (Serviços)**: `Backend/Frontend`

## 2. Fluxo de Dados

O fluxo de dados é unidirecional, desde a coleta no campo até o armazenamento na nuvem.

1.  **Sensor Node -> Bridge Gateway (ESP-NOW)**
    - O `Sensor Node` acorda do `deep sleep`, realiza a leitura dos sensores (e.g., DHT22).
    - Os dados são encapsulados em uma `struct` e enviados via ESP-NOW em modo broadcast ou para o MAC do gateway pareado.
    - O ESP-NOW foi escolhido por sua baixa latência, baixo consumo de energia e por não exigir a sobrecarga de uma conexão Wi-Fi tradicional.
    - Após o envio, o nó volta para `deep sleep` para economizar bateria.

2.  **Bridge Gateway -> Master Gateway (LoRa)**
    - O `Bridge Gateway` permanece em modo de escuta (ESP-NOW).
    - Ao receber um pacote, ele o decodifica.
    - O pacote é então re-codificado para transmissão via LoRa.
    - O LoRa foi escolhido por seu longo alcance (quilômetros) e robustez a interferências, ideal para cobrir áreas extensas entre os pontos de agregação e o gateway principal.

3.  **Master Gateway -> Servidor (MQTT)**
    - O `Master Gateway` permanece em modo de escuta (LoRa).
    - Ao receber um pacote LoRa, ele o decodifica.
    - O gateway se conecta à rede Wi-Fi local (provisionada via Captive Portal).
    - Os dados são formatados (e.g., JSON) e publicados em um tópico MQTT no broker do servidor.
    - O MQTT foi escolhido por ser um protocolo leve, padrão na indústria de IoT, e por seu modelo publish/subscribe, que desacopla o hardware do backend.

4.  **Servidor -> Banco de Dados**
    - O servidor backend (Go) está inscrito (`subscribed`) no tópico MQTT.
    - Ao receber uma mensagem, ele a valida, processa e armazena no banco de dados de série temporal InfluxDB, que é otimizado para esse tipo de carga de trabalho.

## 3. Lógica do Captive Portal (Master Gateway)

O `Master Gateway` precisa se conectar à internet, mas as credenciais de Wi-Fi do usuário final não são conhecidas em tempo de desenvolvimento. O Captive Portal resolve isso de forma elegante.

1.  **Inicialização**: O gateway verifica na NVS (Non-Volatile Storage) se há credenciais de Wi-Fi salvas.
2.  **Modo Provisionamento (Sem Credenciais)**:
    - Se nenhuma credencial for encontrada, o ESP32 inicia em modo `Access Point (AP)`.
    - Ele cria uma rede Wi-Fi aberta com um SSID específico, como `Semea-tec-Gateway-Setup`.
    - Um servidor DNS é ativado no ESP32, configurado para redirecionar todas as requisições de nomes de domínio para o próprio IP do gateway.
    - Um servidor web leve (HTTP) é iniciado na porta 80.
3.  **Interação com o Usuário**:
    - O usuário se conecta à rede Wi-Fi `Semea-tec-Gateway-Setup`.
    - Ao tentar acessar qualquer site, o sistema operacional do celular/notebook detecta o portal cativo e abre uma página web.
    - Essa página é servida pelo ESP32 e contém um formulário para o usuário inserir o SSID e a senha da rede Wi-Fi local.
4.  **Armazenamento e Reinicialização**:
    - Após o envio do formulário, o servidor web no ESP32 recebe as credenciais.
    - As credenciais são salvas de forma segura na NVS.
    - O gateway se reinicia automaticamente.
5.  **Modo Operação Normal**:
    - Na próxima inicialização, o gateway encontra as credenciais na NVS, conecta-se à rede Wi-Fi como um cliente (`station`) e inicia sua operação normal de escuta LoRa e publicação MQTT.

## 4. Abstração de Hardware (HAL)

Para promover a reutilização de código e desacoplar a lógica de aplicação dos detalhes de hardware, adotamos uma Camada de Abstração de Hardware (HAL) na forma de um componente ESP-IDF.

- **Localização**: `/components/hal`
- **Estrutura**: Cada periférico ou sensor (e.g., DHT22, SX1262, SSD1306) terá seus próprios arquivos `hal_NOME.c` e `hal_NOME.h`.
- **Princípio**: A lógica de aplicação (e.g., `sensor_node_main.c`) não deve interagir diretamente com registradores ou bibliotecas de baixo nível (como `driver/gpio.h` ou `driver/i2c.h`). Em vez disso, ela deve chamar funções da HAL.
- **Exemplo de Interface**:

    ```c
    // Exemplo de interface em components/hal/include/hal_sensor_dht.h
    #ifndef HAL_SENSOR_DHT_H
    #define HAL_SENSOR_DHT_H

    #include "esp_err.h"
    #include "driver/gpio.h"

    esp_err_t hal_dht_init(gpio_num_t pin);
    esp_err_t hal_dht_read_data(float* temperatura, float* umidade);

    #endif // HAL_SENSOR_DHT_H
    ```

- **Vantagens**: Se trocarmos o sensor DHT22 por um BME280, apenas o componente `hal` precisa ser modificado. A lógica de aplicação nos firmwares permanece intacta, desde que a nova implementação da HAL satisfaça a mesma interface.