# Especificação de Requisitos - Semea-tec

**Versão: 1.0**

Este documento define os requisitos funcionais (RF) e não-funcionais (RNF) para o projeto Semea-tec. Cada requisito possui um ID único para fins de rastreabilidade.

## 1. Requisitos Funcionais (RF)

| ID    | Requisito                  | Descrição                                                                                                                              | Prioridade |
| :---- | :------------------------- | :------------------------------------------------------------------------------------------------------------------------------------- | :--------- |
| RF-01 | Provisionamento de Wi-Fi   | O `Master Gateway` deve fornecer um Captive Portal para que o usuário configure as credenciais da rede Wi-Fi sem a necessidade de um app. | Alta       |
| RF-02 | Leitura de Sensores        | O `Sensor Node` deve ser capaz de ler dados de temperatura e umidade de um sensor da família DHTxx.                                      | Alta       |
| RF-03 | Otimização de Energia      | O `Sensor Node` deve utilizar o modo `deep sleep` entre as leituras e transmissões para maximizar a vida útil da bateria.                | Alta       |
| RF-04 | Armazenamento de Histórico | O sistema deve armazenar todas as leituras de sensores recebidas, com seu respectivo timestamp, para consulta histórica.                 | Alta       |
| RF-05 | Visualização de Dados      | A aplicação frontend deve exibir os dados históricos em forma de gráficos e os dados mais recentes em formato de medidor (gauge).        | Média      |
| RF-06 | Sistema de Alertas         | O sistema deve ser capaz de notificar o usuário (via UI) quando a bateria de um `Sensor Node` estiver baixa.                             | Média      |

## 2. Requisitos Não-Funcionais (RNF)

| ID     | Requisito             | Métrica                                                                                             | Valor Alvo                                                              |
| :----- | :-------------------- | :-------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| RNF-01 | Autonomia de Bateria  | Vida útil da bateria do `Sensor Node` com um par de pilhas AA.                                      | Mínimo de 6 meses, considerando uma transmissão a cada 30 minutos.      |
| RNF-02 | Escalabilidade        | Número de `Sensor Nodes` que um único `Bridge Gateway` pode suportar.                               | O sistema deve operar de forma estável com até 20 nós por bridge.       |
| RNF-03 | Latência Fim-a-Fim    | Tempo decorrido entre a leitura do sensor e a sua disponibilidade para consulta na API do backend.  | < 15 segundos em condições normais de operação.                         |
| RNF-04 | Segurança             | Proteção dos dados em trânsito.                                                                     | A comunicação Wi-Fi entre o gateway e o roteador deve usar WPA2/WPA3.   |
| RNF-05 | Manutenibilidade      | Aderência aos padrões de código e arquitetura.                                                      | O código deve seguir estritamente as diretrizes do `CONTRIBUTING.md`.   |
| RNF-06 | Confiabilidade da Rede| Taxa de sucesso na entrega de pacotes do `Sensor Node` ao `Master Gateway`.                         | > 95% em um raio de 500m (LoRa) com linha de visada parcial.             |