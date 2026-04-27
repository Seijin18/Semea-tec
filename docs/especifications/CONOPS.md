# Conceito de Operação (CONOPS) - Semea-tec

**Versão: 1.0**

Este documento descreve o conceito de operação do sistema Semea-tec, detalhando a jornada do usuário, o ciclo de vida dos dados e os procedimentos para cenários de manutenção e falha.

## 1. Jornada do Usuário (User Journey)

A experiência do usuário foi projetada para ser simples e direta, desde a aquisição até a operação diária.

### 1.1. Unboxing e Primeira Configuração (Master Gateway)

1.  **Energização**: O usuário conecta o `Master Gateway` a uma fonte de energia USB.
2.  **Modo de Provisionamento**: Ao ser ligado pela primeira vez, o gateway não encontra credenciais de Wi-Fi e entra em modo de provisionamento, criando um Access Point (AP) com o SSID `Semea-tec-Gateway-Setup`.
3.  **Conexão e Captive Portal**: O usuário conecta seu smartphone ou notebook a esta rede Wi-Fi. O sistema operacional detecta o portal cativo e abre automaticamente uma página web.
4.  **Entrada de Credenciais**: A página web (servida pelo próprio ESP32) exibe um formulário onde o usuário insere o SSID e a senha da sua rede Wi-Fi local.
5.  **Reinicialização e Conexão**: Após submeter o formulário, o gateway armazena as credenciais na NVS (Non-Volatile Storage), reinicia e se conecta automaticamente à rede Wi-Fi informada, entrando em modo de operação normal.

### 1.2. Operação e Visualização

1.  **Ativação dos Nós**: O usuário energiza os `Sensor Nodes` e `Bridge Gateways`. Eles são pré-configurados e começam a operar imediatamente.
2.  **Acesso ao Dashboard**: O usuário acessa a aplicação web (frontend) do Semea-tec em seu navegador.
3.  **Visualização de Dados**: Após o login, o usuário é apresentado a um dashboard que exibe os dados de temperatura e umidade em tempo real (ou quase real) e gráficos com o histórico das medições.

## 2. Ciclo de Vida do Dado

O fluxo de dados é o coração do sistema, projetado para eficiência e robustez.

1.  **Geração (Sensor Node)**: O ciclo começa quando o `Sensor Node` acorda do `deep sleep`. Ele invoca a função `hal_dht_read_data()` da nossa HAL para ler os dados de temperatura e umidade.
2.  **Transmissão Local (ESP-NOW)**: Os dados são encapsulados em uma `struct` e transmitidos via ESP-NOW para o `Bridge Gateway` pareado. Após a transmissão, o nó retorna ao `deep sleep` para conservar energia.
3.  **Repasse de Longo Alcance (LoRa)**: O `Bridge Gateway` recebe o pacote ESP-NOW, o decodifica e o retransmite usando o rádio LoRa, garantindo que o sinal alcance o `Master Gateway` a longas distâncias.
4.  **Conexão com a Nuvem (MQTT)**: O `Master Gateway` recebe o pacote LoRa, o decodifica, formata os dados como uma carga útil JSON e os publica em um tópico MQTT específico (ex: `semeatec/sensor/data`) no broker Mosquitto.
5.  **Processamento e Armazenamento (Backend)**: O serviço backend em Go, que está inscrito no tópico, recebe a mensagem JSON. Ele valida os dados, adiciona um timestamp de servidor e os persiste no banco de dados de séries temporais InfluxDB.
6.  **Apresentação (Frontend)**: A aplicação frontend consulta a API exposta pelo backend para buscar os dados (atuais e históricos) e exibi-los de forma intuitiva para o usuário.

## 3. Cenários de Manutenção e Falha

O sistema é projetado com resiliência em mente.

-   **Falha de Conexão (Master Gateway)**: Se a conexão Wi-Fi ou com o broker MQTT for perdida, o gateway tentará se reconectar periodicamente, utilizando uma estratégia de *exponential backoff* para não sobrecarregar a rede. A implementação de um buffer para mensagens offline pode ser considerada em uma `feature/offline-buffering`.

-   **Bateria Baixa (Sensor Node)**: A `struct` de dados enviada pelo `Sensor Node` inclui um campo para a voltagem da bateria. O backend monitora esse valor. Se cair abaixo de um limiar pré-definido, o sistema dispara um alerta para o usuário (via frontend ou e-mail), informando a necessidade de substituição da bateria do nó específico.

-   **Perda de Pacotes (ESP-NOW/LoRa)**: A comunicação via rádio é inerentemente suscetível a perdas. O protocolo atual é do tipo "dispare e esqueça" para maximizar a autonomia. O frontend e o backend devem ser capazes de lidar com lacunas nos dados (ex: não quebrar um gráfico se um ponto estiver faltando).