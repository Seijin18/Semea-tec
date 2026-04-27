# Guia de Contribuição do Semea-tec

Obrigado pelo seu interesse em contribuir com o projeto! Para manter a qualidade e a consistência do código, pedimos que siga as diretrizes abaixo.

## 1. Nomenclatura de Código

Aderimos a um padrão de nomenclatura estrito para facilitar a leitura e a manutenção do código.

- **Variáveis e Nomes de Arquivos**: `snake_case`
  - Exemplo: `int sensor_data;`, `mqtt_client.c`

- **Constantes e Macros**: `UPPER_SNAKE_CASE`
  - Exemplo: `#define MAX_BUFFER_SIZE 1024`, `const int TIMEOUT_MS = 5000;`

- **Structs, Enums e Typedefs**: `PascalCase_t`
  - Exemplo: `typedef struct { ... } SensorData_t;`, `typedef enum { ... } WifiState_t;`

- **Funções**: `modulo_acao_alvo()`
  - O nome da função deve indicar claramente seu módulo (o que ela opera), a ação e o alvo.
  - Exemplo: `wifi_connect_station()`, `dht_read_temperature()`, `lora_send_packet()`

## 2. Fluxo de Trabalho com Git (Git Flow)

Utilizamos um modelo de ramificação baseado no Git Flow para organizar o desenvolvimento.

- **`main`**: Contém o código de produção estável. Só aceita merges de `develop` (para releases) ou `hotfix/`.
- **`develop`**: Branch principal de desenvolvimento. Contém o código com as últimas funcionalidades desenvolvidas. É a base para novas `features`.
- **`feature/<nome-da-feature>`**: Para desenvolvimento de novas funcionalidades.
  - Exemplo: `feature/captive-portal`, `feature/bme280-support`
  - Criada a partir de `develop`.
  - Ao concluir, faz-se o merge de volta para `develop`.

- **`hotfix/<nome-do-hotfix>`**: Para correções críticas em produção.
  - Criada a partir de `main`.
  - Ao concluir, faz-se o merge para `main` e também para `develop`.

- **`refactor/<nome-do-refactor>`**: Para refatorações de código que não adicionam novas funcionalidades nem corrigem bugs.
  - Exemplo: `refactor/hal-interface`

- **`docs/<nome-da-doc>`**: Para adicionar ou atualizar a documentação.
  - Exemplo: `docs/update-architecture-diagram`

- **`ci/<nome-da-tarefa>`**: Para mudanças relacionadas à Integração Contínua (CI/CD).

## 3. Padrão de Mensagens de Commit

Adotamos o padrão **Conventional Commits** para tornar o histórico de commits legível e automatizável.

A estrutura é: `<tipo>(<escopo>): <assunto>`

- **`feat`**: Uma nova funcionalidade.
- **`fix`**: Uma correção de bug.
- **`docs`**: Mudanças na documentação.
- **`style`**: Mudanças que não afetam o significado do código (espaços, formatação, etc.).
- **`refactor`**: Uma mudança de código que não corrige um bug nem adiciona uma funcionalidade.
- **`test`**: Adicionando testes ou corrigindo testes existentes.
- **`chore`**: Mudanças no processo de build ou ferramentas auxiliares.

**Exemplos:**
```
feat(gateway): adicionar suporte para publicação MQTT com QoS 1
fix(sensor): corrigir cálculo de checksum em leituras do DHT
docs(readme): atualizar instruções de setup do ESP-IDF
```