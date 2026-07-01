

# Repositório n8n-workflows

#

# Visão Geral

Este repositório contém uma série de arquivos de automação de workflows do n8n. O n8n é uma ferramenta de automação de workflows que permite criar automações complexas por meio de uma interface visual baseada em nós. Cada workflow é armazenado na forma de um arquivo JSON, contendo definições de nós, conexões e informações de configuração.

#

# Estrutura do Repositório

```text

text

bash
n8n-workflows/
├── workflows/           

# Diretório principal, contém todos os arquivos JSON de workflows do n8n
│   ├── *.json          

# Arquivos individuais de workflow
├── README.md           

# Documentação do repositório
├── claude.md           

# Este arquivo

 - Contexto para o assistente de IA
└── [其他文件]          

# Outros arquivos de configuração ou documentação
```text

text

text

#

# Formato do Arquivo de Workflow

Cada arquivo JSON de workflow contém:

- **name**: identificador do workflow

- **nodes**: array de objetos de nós, define as operações

- **connections**: objeto que define a forma de conexão entre os nós

- **settings**: configuração no nível do workflow

- **staticData**: dados persistentes entre execuções

- **tags**: tags de categorização

- **createdAt/updatedAt**: timestamps

#

# Tipos Comuns de Nós

- **Nós de gatilho**: webhook, cron, manual

- **Nós de integração**: HTTP Request, conectores de banco de dados, integrações de API

- **Nós de lógica**: IF, Switch, Merge, Loop

- **Nós de dados**: Function, Set, Transform Data

- **Nós de comunicação**: Email, Slack, Discord, etc.

#

# Usando Este Repositório

#

#

# Recomendações para Tarefas de Análise

Ao analisar os workflows deste repositório:

1. Faça o parse dos arquivos JSON para entender a estrutura do workflow

2. Examine a cadeia de nós para determinar a implementação da funcionalidade

3. Identifique integrações e dependências externas

4. Considere a lógica de negócio implementada pelas conexões dos nós

#

#

# Recomendações para Tarefas de Documentação

Ao documentar workflows:

1. Verifique a consistência das descrições existentes com a implementação real

2. Identifique os mecanismos de gatilho e os planos de agendamento

3. Liste todos os serviços e APIs externos utilizados

4. Registre as transformações de dados e a lógica de negócio

5. Destaque quaisquer mecanismos de tratamento de erros ou de repetição

#

#

# Recomendações para Tarefas de Modificação

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

- Trate com cautela quaisquer valores ou endpoints codificados diretamente (hardcoded)

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

- **Pipeline de dados**: Gatilho → Buscar Dados → Transformar → Armazenar/Enviar

- **Sincronização de integração**: Tarefa agendada → Chamada de API → Comparar → Atualizar Sistemas

- **Automação**: Webhook → Processar → Lógica Condicional → Executar Ações

- **Monitoramento**: Agenda → Verificar Status → Alerta de Problema

#

# Contexto Útil para o Assistente de IA

Ao auxiliar com este repositório:

1. **Análise de workflow**: entenda o propósito de negócio examinando o fluxo dos nós, e não apenas os nós individuais.

2. **Geração de documentação**: crie descrições que expliquem a funcionalidade que o workflow realiza, e não apenas quais nós ele contém.

3. **Solução de problemas**: problemas comuns incluem:

   - Conexões de nós incorretas

   - Ausência de tratamento de erros

   - Processamento de dados ineficiente em loops

   - Valores hardcoded que deveriam ser parametrizados

4. **Sugestões de otimização**:

   - Identificar operações redundantes

   - Sugerir processamento em lote em cenários aplicáveis

   - Recomendar a adição de tratamento de erros

   - Sugerir a divisão de workflows complexos

5. **Geração de código**: ao criar ferramentas para analisar esses workflows:

   - Lide com várias versões do formato do n8n

   - Considere nós personalizados

   - Faça o parse de expressões nos parâmetros dos nós

   - Considere a ordem de execução dos nós

#

# Informações Específicas do Repositório

[Adicione aqui quaisquer informações específicas sobre workflows, convenções de nomenclatura ou considerações especiais]

#

# Compatibilidade de Versão

- Versão do n8n: [Especifique a versão do n8n com a qual estes workflows são compatíveis]

- Última atualização: [Data da última atualização importante]

- Notas de migração: [Quaisquer considerações específicas de versão]
