# AUTOMAÇÃO DIGITAL · CENTRO DE EXCELÊNCIA

# PDD: Process Definition Document

**Documento de definição do processo — Rascunho para validação pelo CoE**  
**Origem da análise:** workflow `Simulacao CoE - Fluxo para revisao`, exportado do n8n.  
**Critério de preenchimento:** **[FATO]** = evidência explícita no JSON; **[A CONFIRMAR]** = hipótese, proposta ou informação que depende do demandante. Na ausência de evidência, utiliza-se **não identificável no fluxo**.

> **Limitação central:** o JSON descreve um fluxo técnico automatizado, **não** o procedimento manual anterior. Assim, a seção AS IS não pode ser considerada um levantamento validado do processo atual de negócio.

---

## 1. IDENTIFICAÇÃO

| Campo | Preenchimento |
|---|---|
| **Nome do processo** | Consulta e Registro de Associados Ativos **[A CONFIRMAR]** |
| **Área demandante** | Gestão de Associados / Operações de Cooperativa **[A CONFIRMAR]** |
| **Autor do PDD** | não identificável no fluxo |
| **Data / versão** | 11/11/2026 · v0.1 (data e versão deste rascunho) |
| **Prioridade** | não identificável no fluxo |
| **Status** | Rascunho — pendente de validação pelo demandante e CoE |

**Tecnologia observada:** n8n com nós de acionamento manual, requisições HTTP e preparação de campos **[FATO]**.

## 2. OBJETIVO E JUSTIFICATIVA

**Objetivo provável [A CONFIRMAR]:** consultar registros de associados com `status=ativo` e transmitir determinados dados a um endpoint de registros, com possível redução de atividades manuais.

**Evidência [FATO]:** existe uma requisição de consulta ao recurso `/associados`, seguida por outra de envio ao recurso `/registros` com os campos `associado` e `valor`.

**Dor de negócio, justificativa da automação e resultado esperado:** não identificável no fluxo. Qualquer alegação de economia de tempo, redução de retrabalho ou melhoria de qualidade dependerá de levantamento com o demandante **[A CONFIRMAR]**.

## 3. PROCESSO ATUAL (AS IS), PASSO A PASSO

**Processo manual anterior:** **não identificável no fluxo**. Não há evidências sobre pessoas executoras, telas utilizadas manualmente, aprovações ou operações prévias à automação.

**Sequência efetivamente implementada no n8n (referência técnica, não levantamento de AS IS):**

1. **Acionamento manual [FATO]:** o nó `node` (*Manual Trigger*) inicia uma execução por acionamento do operador.
2. **Consulta [FATO]:** o nó `HTTP Request1` solicita o recurso `/associados` usando `status=ativo`. Uma chave de API está incluída diretamente na URL; seu valor não é reproduzido neste PDD.
3. **Preparação de campos [FATO]:** o nó `dados` (*Set*) acrescenta `db_user`, `db_password` e `conn`, com credenciais embutidas e preservação dos demais campos recebidos. **Não há uso comprovado da conexão com banco de dados.**
4. **Envio [FATO]:** o nó `HTTP Request2` envia requisição `POST` ao recurso `/registros`, com mapeamento `associado ← $json.nome` e `valor ← $json.valor`.
5. **Marcação final [FATO]:** o nó `fazer` (*Set*) adiciona `x = "ok"`, o que **não é evidência independente** de registro bem-sucedido no sistema destinatário.

**Lacunas:** formato da resposta de consulta, conteúdo dos registros, etapas humanas existentes, forma de confirmação funcional e resultado final de negócio: **não identificável no fluxo**.

## 4. REGRAS DE NEGÓCIO

- **Filtro técnico observado [FATO]:** a consulta inclui `status=ativo`.
- **Regra que justificaria consultar apenas ativos:** **[A CONFIRMAR]**. O filtro técnico não prova, por si só, uma política de elegibilidade de negócio.
- **Validações de campos, limites, prazos, aprovações e regras de cálculo:** **não identificável no fluxo**.
- **Significado, origem e regras aplicáveis ao campo `valor`:** **não identificável no fluxo**.
- **Política de duplicidade e idempotência:** **não identificável no fluxo**.

> O mapeamento de variáveis para o `POST` é uma **etapa técnica**, não uma regra de negócio validada.

## 5. EXCEÇÕES E COMO TRATAR

**Situação atual [FATO]:** o JSON não apresenta ramos de exceção, política explícita de repetição (*retry*), controle de duplicidade ou notificações de falha.

| Exceção possível | Tratamento encontrado no fluxo | Tratamento a definir com o demandante |
|---|---|---|
| API de consulta indisponível, com erro ou *timeout* | Nenhum tratamento específico explícito **[FATO]** | Repetição, escalonamento e registro de falha **[A CONFIRMAR]** |
| API de registros rejeita o envio | Nenhum tratamento específico explícito **[FATO]** | Critério de sucesso, repetição e responsável pelo acompanhamento **[A CONFIRMAR]** |
| `nome` ou `valor` ausente/inválido | Nenhuma validação explícita **[FATO]** | Regra de validação e rejeição **[A CONFIRMAR]** |
| Registro já enviado anteriormente | Nenhuma verificação explícita **[FATO]** | Identificador único e prevenção de duplicidade **[A CONFIRMAR]** |
| Falha de autenticação | Nenhum caminho específico explícito **[FATO]** | Gestão de credenciais e resposta operacional **[A CONFIRMAR]** |

**Observação:** o comportamento padrão da plataforma n8n não deve ser confundido com tratamento formal de exceções definido para o processo.

## 6. SISTEMAS, DADOS E LGPD

| Campo | Preenchimento |
|---|---|
| **Sistemas envolvidos** | n8n; API de consulta `/associados`; API de envio `/registros` **[FATO]**. Uma string de conexão de banco é criada como texto, **sem conexão efetiva demonstrada** **[FATO]**. |
| **Entradas (dados)** | Dados retornados por `/associados?status=ativo`, com formato e campos da resposta **não identificáveis no fluxo**; referência posterior a `$json.nome` e `$json.valor` **[FATO]**. |
| **Saídas (dados)** | Requisição `POST` contendo os campos `associado` e `valor` **[FATO]**. Retorno de confirmação e persistência final: **não identificável no fluxo**. O campo técnico `x = "ok"` é acrescentado ao fim **[FATO]**. |
| **Dados pessoais / sensíveis** | `nome` pode identificar pessoa física **[A CONFIRMAR]**; `valor` e `status` podem ser dados pessoais se vinculados a indivíduo identificável **[A CONFIRMAR]**. Dados pessoais sensíveis (categoria específica da LGPD): **não identificáveis no fluxo**. |

**Segurança das informações [FATO]:** o fluxo exportado expõe **chave de API**, **senha de banco de dados** e **string de conexão contendo credenciais**. Valores deliberadamente omitidos neste PDD. Caso sejam credenciais reais, devem ser revogadas/rotacionadas e migradas para armazenamento seguro.

**Proteção de dados [A CONFIRMAR]:** base legal, finalidade específica do tratamento, responsabilidades de controlador/operador, minimização, retenção, controles de acesso, compartilhamento e procedimentos de exclusão. Essas práticas não são comprovadas no fluxo.

## 7. VOLUME E FREQUÊNCIA

| Campo | Preenchimento |
|---|---|
| **Volume** | não identificável no fluxo |
| **Frequência** | Acionamento manual pelo nó *Manual Trigger* **[FATO]**; periodicidade de execução na operação: **não identificável no fluxo** |
| **Janela** | não identificável no fluxo |

**Observações [FATO]:** não há agendamento automático configurado. A quantidade de associados por execução e o comportamento com vários itens de resposta não são comprovados pelo arquivo exportado.

## 8. BENEFÍCIO ESPERADO

**Possíveis benefícios [A CONFIRMAR]:** menos transferência manual de informações, padronização de envios, redução de erros e maior rastreabilidade, **desde que** sejam verificados o processo real e os mecanismos de controle necessários.

- **Horas economizadas:** não identificável no fluxo.
- **Erros evitados:** não identificável no fluxo.
- **Custos de operação/implantação:** não identificável no fluxo.
- **ROI aproximado:** não calculável com os dados disponíveis.

A estimativa para o *business case* requer medidas do processo manual, quantidade de operações, taxas de erro e custos **[A CONFIRMAR]**.

## 9. CRITÉRIOS DE ACEITAÇÃO (DEFINITION OF DONE)

Os critérios abaixo **constam no template do CoE** e são considerados **critérios propostos de aceitação**, não evidências de que a automação os atenda:

- [ ] **O robô cobre o caminho feliz e as exceções listadas.** Caminho básico de execução visível; tratamento de exceções não comprovado **[FATO]**.
- [ ] **Credenciais no cofre, nada escrito no fluxo.** **Não atendido no artefato revisado [FATO]**: há segredos escritos diretamente na configuração.
- [ ] **Logs e trilha de auditoria da execução.** Não há implementação explícita de logs estruturados de negócio **[FATO]**; requisitos detalhados **[A CONFIRMAR]**.
- [ ] **PDD e SDD entregues e revisados pelo CoE.** Este documento é um rascunho de PDD; SDD e aprovação **não identificáveis no fluxo**.

**Critérios adicionais [A CONFIRMAR]:** validação de campos, identificação única dos registros, confirmação da resposta da API, evidências de testes e responsável pela homologação.

## 10. RISCOS E DEPENDÊNCIAS

| Risco ou dependência | Evidência / situação | Encaminhamento proposto |
|---|---|---|
| **Credenciais expostas** | Segredos inseridos diretamente na URL e no nó `dados` **[FATO]** | Rotacionar se reais, usar cofre/credenciais do n8n e restringir acesso **[A CONFIRMAR]** |
| **Registros duplicados** | Não há controle explícito de idempotência **[FATO]** | Definir chave de unicidade e comportamento em reprocessamentos **[A CONFIRMAR]** |
| **Falhas sem resposta operacional definida** | Ausência de rotas específicas de erro, alerta e *retry* **[FATO]** | Definir política de falhas e responsável **[A CONFIRMAR]** |
| **Ausência de comprovação de registro** | O indicador final é `x = "ok"`, sem conferência funcional independente **[FATO]** | Definir confirmação por resposta/consulta da API **[A CONFIRMAR]** |
| **Dados de origem incertos** | Esquema de resposta de `/associados` não consta no JSON **[FATO]** | Confirmar disponibilidade de `nome` e `valor` **[A CONFIRMAR]** |
| **Nomenclatura genérica e documentação incompleta** | Nós chamados `node`, `HTTP Request1`, `HTTP Request2`, `dados` e `fazer`; nota de revisão no workflow **[FATO]** | Padronizar nomes e documentar o processo **[A CONFIRMAR]** |
| **Conformidade LGPD não demonstrada** | Não há base legal, prazo de retenção nem controles específicos documentados no fluxo **[FATO]** | Revisar com responsáveis por privacidade **[A CONFIRMAR]** |
| **Dependência de endpoints externos** | São utilizados os recursos `/associados` e `/registros` **[FATO]** | Validar responsáveis, disponibilidade e contrato das APIs **[A CONFIRMAR]** |

---

## ANEXO A — SUPOSIÇÕES A VALIDAR

1. O processo é utilizado por uma cooperativa que gerencia associados **[A CONFIRMAR]**.
2. A área demandante é responsável pela gestão ou operação desses associados **[A CONFIRMAR]**.
3. O objetivo de negócio é transferir informações de associados ativos para um serviço de registros **[A CONFIRMAR]**.
4. O campo `valor` representa um atributo relevante para o negócio, possivelmente financeiro **[A CONFIRMAR]**.
5. Existe um processo manual anterior que pode ser reduzido ou substituído **[A CONFIRMAR]**.
6. Os dados de `nome` e `valor` realmente constam nos itens retornados pela consulta **[A CONFIRMAR]**.
7. Os registros enviados precisam ser persistidos e auditáveis no destino **[A CONFIRMAR]**.

## ANEXO B — PERGUNTAS AO DEMANDANTE

1. Qual é o **nome oficial** do processo, qual a **área demandante** e quem é seu responsável?
2. Como o **processo manual (AS IS)** é realizado hoje, passo a passo?
3. Qual problema de negócio a automação resolve e quais resultados são esperados?
4. O filtro `status=ativo` representa uma regra de negócio? Há outras condições?
5. O que significa `valor`, como é obtido e quais validações são necessárias?
6. Qual é o esquema real da resposta da consulta? Todo item contém `nome` e `valor`?
7. O registro deve ser feito para cada associado? Como evitar duplicidades em reprocessamentos?
8. Como comprovar que cada `POST` foi registrado com sucesso no sistema de destino?
9. O que fazer em falhas de rede, API, autenticação e dados inválidos? Quem recebe os alertas?
10. Para que servem as credenciais de banco presentes no fluxo, já que não há acesso ao banco demonstrado?
11. Qual o volume médio, a frequência desejada e a janela permitida para execução?
12. Há tratamento de dados de pessoas físicas? Qual base legal, finalidade, retenção e política de acesso?
13. Quais indicadores (tempo, erros, custo) permitirão estimar o benefício e o ROI?
14. Quem homologa os testes, revisa o SDD e aprova o uso em produção?

---

**Parecer preliminar do CoE:** manter como **rascunho**. O fluxo apresenta um caminho de consulta e envio, mas não comprova o processo manual AS IS nem os controles necessários para aprovação. A nota interna do workflow descreve a atividade como **simulação de revisão**, solicitando identificar e documentar problemas antes de corrigir; ela não constitui uma etapa operacional do processo.
