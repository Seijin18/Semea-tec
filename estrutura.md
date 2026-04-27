Prompt: Criação de Documentação de Engenharia (Semea-tec)
"Atue como um Engenheiro de Sistemas Sênior. Com base na estrutura do projeto Semea-tec (IoT com ESP32, ESP-NOW, LoRa e MQTT), gere quatro documentos técnicos fundamentais na pasta docs/specifications/.

Siga as diretrizes abaixo para cada arquivo, garantindo que o conteúdo seja profissional, técnico e fácil de entender:

1. Conceito de Operação (docs/specifications/CONOPS.md)
Descreva o ciclo de vida do produto. Inclua:

User Journey: Desde o 'unboxing' e a configuração do Wi-Fi via Captive Portal até a visualização dos dados no App.

Ciclo de Dados: Como o dado nasce no sensor, passa pela malha (ESP-NOW -> LoRa -> MQTT) e chega ao banco de dados.

Manutenção: Como o sistema se comporta em caso de falha de conexão ou bateria baixa.

2. Documento de Arquitetura Detalhada (docs/specifications/ARCHITECTURE.md)
Descreva a topologia de rede (Estrela para ESP-NOW, Ponto-a-ponto para LoRa, Client-Server para MQTT).

Detalhe a HAL (Hardware Abstraction Layer): explique como os drivers de sensores devem ser isolados da lógica de aplicação.

Descreva a stack do servidor: Backend em Go, Banco InfluxDB e o papel do Mosquitto (MQTT Broker).

3. Especificação de Requisitos (docs/specifications/REQUIREMENTS.md)
Divida em tabelas com IDs únicos (ex: RF-01, RNF-01):

Requisitos Funcionais (RF): Captive Portal, leitura de sensores, armazenamento histórico, alertas no celular, suporte a Deep Sleep.

Requisitos Não-Funcionais (RNF): Escalabilidade (suporte a 'n' dispositivos), autonomia de bateria (mínimo de 6 meses), latência de comunicação e segurança básica.

4. Plano de Testes (docs/specifications/TEST_PLAN.md)
Crie um roteiro para validar o projeto:

Testes Unitários: Validação da HAL e funções de conversão de dados.

Testes de Integração: Sucesso da ponte ESP-NOW para LoRa e do LoRa para o servidor.

Testes de Estresse: Como validar o comportamento do sistema com um alto volume de dados (simulando múltiplos dispositivos).

Testes de Campo: Validação do alcance do rádio LoRa e eficiência da carga solar.

Instruções Adicionais:

Use Markdown com tabelas e listas para facilitar a leitura.

Garanta que os documentos mencionem os padrões de nomenclatura (snake_case, etc.) e o fluxo de Git (branches feature/, hotfix/, etc.) que já estabelecemos.

Ao final, sugira uma Matriz de Rastreabilidade que ligue os Requisitos aos Testes."