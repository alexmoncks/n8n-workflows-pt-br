

# Repositório n8n-workflows

#

# Visão Geral
Este repositório contém uma coleção de arquivos de automação de workflows do n8n. O n8n é uma ferramenta de automação de workflows que permite criar automações complexas por meio de uma interface visual baseada em nós. Cada workflow é armazenado como um arquivo JSON contendo definições de nós, conexões e configurações.

#

# Estrutura do Repositório
```text

text

text
n8n-workflows/
├── workflows/           

# Diretório principal contendo todos os arquivos JSON de workflows do n8n
│   ├── *.json          

# Arquivos individuais de workflow
├── README.md           

# Documentação do repositório
├── claude.md           

# Este arquivo

 - Contexto para o assistente de IA
└── [other files]       

# Arquivos adicionais de configuração ou documentação
```text

text

text

#

# Formato do Arquivo de Workflow
Cada arquivo JSON de workflow contém:

- **name**: Identificador do workflow

- **nodes**: Array de objetos de nós que definem operações

- **connections**: Objeto que define como os nós estão conectados

- **settings**: Configuração no nível do workflow

- **staticData**: Dados persistentes entre execuções

- **tags**: Tags de categorização

- **createdAt/updatedAt**: Timestamps

#

# Tipos Comuns de Nós

- **Nós de Gatilho**: webhook, cron, manual

- **Nós de Integração**: HTTP Request, conectores de banco de dados, integrações de API

- **Nós de Lógica**: IF, Switch, Merge, Loop

- **Nós de Dados**: Function, Set, Transform Data

- **Comunicação**: Email, Slack, Discord, etc.

#

# Trabalhando com Este Repositório

#

#

# Para Tarefas de Análise
Ao analisar workflows neste repositório:

1. Faça o parse dos arquivos JSON para entender a estrutura do workflow

2. Examine as cadeias de nós para determinar a funcionalidade

3. Identifique integrações e dependências externas

4. Considere a lógica de negócio implementada pelas conexões dos nós

#

#

# Para Tarefas de Documentação
Ao documentar workflows:

1. Verifique as descrições existentes em relação à implementação real

2. Identifique os mecanismos de gatilho e as agendas

3. Liste todos os serviços e APIs externos utilizados

4. Anote as transformações de dados e a lógica de negócio

5. Destaque quaisquer mecanismos de tratamento de erros ou de repetição

#

#

# Para Tarefas de Modificação
Ao modificar workflows:

1. Preserve a estrutura JSON e os campos obrigatórios

2. Mantenha a unicidade do ID dos nós

3. Atualize as conexões ao adicionar/remover nós

4. Teste a compatibilidade com os requisitos de versão do n8n

#

# Considerações Importantes

#

#

# Segurança

- Os arquivos de workflow podem conter informações sensíveis em URLs de webhook ou configurações de API

- As credenciais normalmente são armazenadas separadamente no n8n, e não nos arquivos de workflow

- Tenha cautela com quaisquer valores ou endpoints codificados diretamente (hardcoded)

#

#

# Boas Práticas

- Os workflows devem ter nomes claros e descritivos

- Workflows complexos se beneficiam de nós de documentação ou comentários

- Nós de tratamento de erros aumentam a confiabilidade

- Workflows modulares (que chamam sub-workflows) melhoram a manutenibilidade

#

#

# Padrões Comuns

- **Pipeline de Dados**: Gatilho → Buscar Dados → Transformar → Armazenar/Enviar

- **Sincronização de Integração**: Cron → Chamada de API → Comparar → Atualizar Sistemas

- **Automação**: Webhook → Processar → Lógica Condicional → Ações

- **Monitoramento**: Agenda → Verificar Status → Alertar em Caso de Problemas

#

# Contexto Útil para Assistentes de IA

Ao auxiliar com este repositório:

1. **Análise de Workflow**: Concentre-se em entender o propósito de negócio examinando o fluxo dos nós, e não apenas os nós individuais.

2. **Geração de Documentação**: Crie descrições que expliquem o que o workflow realiza, e não apenas quais nós ele contém.

3. **Solução de Problemas**: Problemas comuns incluem:

   - Conexões de nós incorretas

   - Ausência de tratamento de erros

   - Processamento de dados ineficiente em loops

   - Valores hardcoded que deveriam ser parâmetros

4. **Sugestões de Otimização**:

   - Identificar operações redundantes

   - Sugerir processamento em lote quando aplicável

   - Recomendar a adição de tratamento de erros

   - Propor a divisão de workflows complexos

5. **Geração de Código**: Ao criar ferramentas para analisar esses workflows:

   - Lide com várias versões do formato do n8n

   - Considere nós personalizados

   - Faça o parse de expressões nos parâmetros dos nós

   - Considere a ordem de execução dos nós

#

# Informações Específicas do Repositório
[Adicione aqui quaisquer informações específicas sobre seus workflows, convenções de nomenclatura ou considerações especiais]

#

# Compatibilidade de Versão

- Versão do n8n: [Especifique a versão do n8n com a qual estes workflows são compatíveis]

- Última atualização: [Data da última atualização importante]

- Notas de migração: [Quaisquer considerações específicas de versão]

-

-

-

[中文](./CLAUDE_ZH.md)