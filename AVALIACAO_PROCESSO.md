# Lab 05: Ficha de Avaliação de RPA e Viabilidade

## 📋 Informações do Aluno
- **Nome:** Nicolas Ferreira Damasceno
- **RA:** 6326314
- **Cenário Escolhido:** Cenário A (Conciliação Bancária Diária)

---

## 🔍 1. Análise de Viabilidade (Matriz de Qualificação)

| Critério de Elegibilidade | Avaliação (Sim/Não) | Justificativa Técnico-Operacional |
| :--- | :---: | :--- |
| **Altamente Repetitivo?** | Sim | O processo ocorre diariamente, exigindo a execução manual dos mesmos passos de download e comparação de dados. |
| **Baseado em Regras Claras?** | Sim | A validação não exige julgamento humano. Ela segue regras exatas de correspondência: correspondência de CNPJ e valor. |
| **Dados de Entrada Estruturados?** | Sim | Os dados vêm de um arquivo `.csv` (extrato bancário) e de campos definidos do sistema ERP, ambos formatos de fácil leitura por robôs. |
| **Processo Estável?** | Sim | As etapas de conciliação bancária padrão e as regras de negócio de match financeiro mudam com pouca frequência. |

**Conclusão de Elegibilidade:** **Elegível para RPA**. O processo cumpre todos os requisitos técnicos para automação, apresentando baixo risco de exceções subjetivas e alto potencial de ganho de tempo e redução de erros humanos.

---

## 📄 2. PDD Simplificado (Process Definition Document)

### 2.1. Visão Geral do Processo
O processo consiste em acessar o portal bancário, realizar o download do extrato diário em formato `.csv`, ler as transações e compará-las com os registros de baixa do sistema ERP interno utilizando o CNPJ e o Valor da transação como chaves de validação.

### 2.2. Volume e Frequência
- **Frequência:** Diária.
- **Volume Estimado:** 150 transações por dia.

### 2.3. Mapeamento do Processo (Passo a Passo)

1. **Início do Processo**: O robô inicia a execução de forma agendada às 08:00 AM.
2. **Download do Extrato**: O robô faz login no portal do banco, navega até a área de relatórios e baixa o arquivo `.csv` do dia anterior.
3. **Leitura de Dados**: O robô abre o arquivo `.csv` e armazena os dados (Data, CNPJ, Valor) em memória.
4. **Consulta no ERP**: O robô faz login no sistema ERP da empresa e extrai a lista de contas a receber com status de "baixa pendente".
5. **Conciliação (Regra de Match)**:
   - Para cada linha do extrato bancário, o robô procura uma linha correspondente no ERP onde o **CNPJ** AND **Valor** sejam idênticos.
   - **Se houver match (Sucesso)**: O robô altera o status no ERP para "Conciliado" e insere a data do extrato.
   - **Se NÃO houver match (Exceção de Negócio)**: O robô pula a linha e marca o registro em um relatório de erros para análise humana.
6. **Fim do Processo**: O robô envia um e-mail para a equipe financeira com o relatório consolidado (Sucessos vs. Exceções).

### 2.4. Gestão de Exceções
- **Exceção de Sistema**: Se o portal do banco estiver fora do ar, o robô tenta novamente 3 vezes (intervalos de 5 minutos). Se persistir, envia um alerta para o TI por e-mail.
- **Exceção de Negócio**: Valores divergentes ou CNPJs não encontrados no ERP não travam o robô. Eles são salvos em uma planilha de "Divergências" anexa ao e-mail final para auditoria manual.
