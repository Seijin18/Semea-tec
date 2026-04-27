# Plano de Testes - Semea-tec

**Versão: 1.0**

Este documento descreve a estratégia de testes para o projeto Semea-tec, garantindo que todos os requisitos sejam atendidos e que o sistema seja robusto e confiável.

## 1. Estratégia de Testes

Adotaremos uma abordagem baseada na pirâmide de testes, com uma base sólida de testes unitários, seguida por testes de integração e um número menor de testes de ponta a ponta (E2E) e de campo.

## 2. Tipos de Teste

### 2.1. Testes Unitários

-   **Objetivo**: Validar a menor porção de código (uma função ou um método) de forma isolada.
-   **Escopo**: Funções dentro da **HAL** (ex: `hal_dht_read_data`), funções de conversão de dados, parsers.
-   **Ferramentas**: Framework Unity, integrado ao ESP-IDF (`idf.py test`).
-   **Exemplo**: Um teste para `hal_dht_read_data` que simula (mock) os níveis de sinal no pino GPIO e verifica se a função retorna os valores de temperatura e umidade esperados ou um erro apropriado.

### 2.2. Testes de Integração

-   **Objetivo**: Validar a comunicação e a interação correta entre dois ou mais componentes do sistema.
-   **Escopo**:
    -   **Integração Hardware**: `Sensor Node` -> `Bridge Gateway` (ESP-NOW para LoRa).
    -   **Integração Cloud**: `Master Gateway` -> Broker MQTT -> Backend -> Banco de Dados.
-   **Exemplo**: Montar um setup com os três dispositivos de hardware. Enviar um valor conhecido (ex: 25.0°C) a partir do `Sensor Node` e verificar, usando um cliente MQTT, se a mensagem JSON correta chega ao broker.

### 2.3. Testes de Estresse (Carga)

-   **Objetivo**: Avaliar a performance e a estabilidade do sistema sob carga intensa.
-   **Escopo**: Principalmente o `Master Gateway` e a stack do `Backend`.
-   **Exemplo**: Desenvolver um script (ex: Python com `paho-mqtt`) que simule 100 `Master Gateways` publicando dados a cada 5 segundos. Monitorar o consumo de CPU/memória do serviço Go e a latência de inserção no InfluxDB para identificar gargalos.

### 2.4. Testes de Campo (E2E)

-   **Objetivo**: Validar o sistema completo em um ambiente de operação real.
-   **Escopo**: Todo o ecossistema Semea-tec.
-   **Exemplo**: Instalar o sistema em uma área externa (ex: fazenda, parque). Medir o alcance máximo efetivo do LoRa com e sem linha de visada. Aferir o consumo real da bateria do `Sensor Node` ao longo de várias semanas para validar o requisito `RNF-01`.

## 3. Matriz de Rastreabilidade de Requisitos (RTM)

A matriz abaixo serve como um elo entre os requisitos especificados e os testes planejados, garantindo que todos os requisitos tenham uma estratégia de validação correspondente.

| ID do Requisito | Descrição Breve do Requisito | ID(s) do Caso de Teste (Exemplo) | Tipo de Teste Associado |
| :-------------- | :---------------------------- | :------------------------------- | :---------------------- |
| RF-01           | Provisionamento via Captive Portal | TST-CP-01, TST-CP-02             | Integração, E2E         |
| RF-02           | Leitura de Sensor DHT         | TST-HAL-DHT-01                   | Unitário                |
| RF-03           | Otimização com Deep Sleep     | TST-FIELD-BATT-01                | Campo                   |
| RF-04           | Armazenamento de Histórico    | TST-INT-MQTT-01                  | Integração              |
| RNF-01          | Autonomia de Bateria          | TST-FIELD-BATT-01                | Campo                   |
| RNF-02          | Escalabilidade                | TST-STRESS-MQTT-01               | Estresse                |
| RNF-03          | Latência Fim-a-Fim            | TST-E2E-LATENCY-01               | E2E / Campo             |
| RNF-06          | Confiabilidade da Rede LoRa   | TST-FIELD-LORA-RANGE-01          | Campo                   |