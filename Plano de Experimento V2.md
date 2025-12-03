# Plano de Experimento – Scoping e Planejamento

## 1. Identificação básica

### 1.1 Título do experimento
**Análise do Impacto de Refatorações na Evolução de Complexidade e Desempenho em Firmware Automotivo: Um Estudo Longitudinal de Mineração de Repositório do Projeto Speeduino (2017-2024)**

### 1.2 ID / código
`EXP-MES-2025-SPEEDUINO-REFACTORING`

### 1.3 Versão do documento e histórico de revisão
* **Versão Atual:** v4.0 (Versão Mineração de Repositório)
* **Histórico:**
    * v1.0 (19/11/2025): Esboço inicial do escopo (abordagem observacional simples).
    * v2.0 (25/11/2025): Expansão do GQM e métricas.
    * v3.0 (02/12/2025): Detalhamento completo do desenho experimental, hipóteses estatísticas e protocolo de execução. + Pivô metodológico - migração para abordagem de Mining Software Repositories com análise de impacto de refatorações históricas.

### 1.4 Datas (criação, última atualização)
* **Criação:** 19/11/2025
* **Última atualização:** 02/12/2025

### 1.5 Autores (nome, área, contato)
* **Autor:** Luiz Filipe Nery Costa
* **Área:** Engenharia de Software / Sistemas Embarcados
* **Contato:** lfncosta@sga.pucminas.br

### 1.6 Responsável principal (PI / dono do experimento)
* **Investigador Principal:** Luiz Filipe Nery Costa
* **Orientador Acadêmico:** Danilo

### 1.7 Projeto / produto / iniciativa relacionada
Este experimento está vinculado ao projeto de código aberto **Speeduino** (sistema de injeção eletrônica e ignição programável). Ele constitui o artefato principal da disciplina de **Medição e Experimentação de Software** e serve como fundamentação metodológica para o Trabalho de Conclusão de Curso (TCC) focado na relação entre práticas de refatoração, evolução de complexidade e desempenho em sistemas críticos embarcados.

---

## 2. Contexto e problema

### 2.1 Descrição do problema / oportunidade
O problema central abordado é a **degradação de desempenho em sistemas de tempo real** causada pelo fenômeno de *Software Bloat* (inchaço de software). Em sistemas embarcados automotivos críticos, como ECUs (Unidades de Controle do Motor), a frequência de operação (*Loop Rate*) é crucial para garantir amostragem adequada de sensores e acionamento preciso de atuadores.

Observações empíricas da comunidade Speeduino indicam que versões recentes do firmware apresentam *Loop Rate* inferior (~250 Hz) comparado a versões antigas (~400 Hz) quando executadas no mesmo hardware (Arduino Mega 2560). A **oportunidade** deste estudo é investigar se **práticas de refatoração realizadas ao longo da evolução do projeto** conseguiram mitigar ou reverter essa degradação, e quais tipos de refatoração tiveram maior impacto em complexidade e desempenho.

Diferentemente de abordagens experimentais que propõem novas refatorações, este estudo utiliza **mineração de repositório** para analisar retrospectivamente o impacto de refatorações já realizadas pela comunidade de desenvolvedores ao longo de 7 anos de evolução.

### 2.2 Contexto organizacional e técnico
* **Organização:** Estudo conduzido no âmbito acadêmico (Laboratório de Engenharia de Software da PUC Minas).
* **Domínio:** Sistemas Embarcados Automotivos (*Automotive Embedded Systems*) - Hard Real-Time.
* **Projeto Analisado:** Speeduino (Open Source, licença GPL, desenvolvido colaborativamente via GitHub).
* **Tecnologias:** 
    * Linguagem: C/C++ (Arduino Framework)
    * Arquitetura: AVR (8-bit, ATmega2560)
    * Compilador: avr-gcc
* **Ferramentas:**
    * *Git:* Mineração de histórico de commits
    * *Lizard:* Análise estática de complexidade ciclomática
    * *TunerStudio MS:* Telemetria e medição de Loop Rate
    * *Arduino IDE:* Compilação de versões históricas

### 2.3 Trabalhos e evidências prévias (internos e externos)
* **Literatura Externa:** 
    * As *Leis de Lehman* (especificamente a lei da "Complexidade Crescente") afirmam que a complexidade de um sistema aumenta ao longo do tempo a menos que haja trabalho ativo de simplificação.
    * Estudos de *Mining Software Repositories* (MSR) demonstram que a análise de commits pode revelar padrões de evolução e impacto de práticas de desenvolvimento.
    * Pesquisas sobre refatoração (Fowler, 1999) propõem que melhorias estruturais no código devem preservar ou melhorar o comportamento observável.
* **Evidências da Comunidade:** 
    * Fóruns do Speeduino relatam que versões pré-2020 apresentavam *Loop Rate* superior a 350 Hz, enquanto versões recentes operam entre 240-280 Hz no mesmo hardware.
    * Commits recentes incluem mensagens como "refactor fuel calculation" e "simplify decoder logic", sugerindo esforços de melhoria estrutural.
* **Lacuna:** Não existe análise sistemática quantificando se essas refatorações melhoraram efetivamente a performance em sistemas embarcados automotivos.

### 2.4 Referencial teórico e empírico essencial
* **Complexidade Ciclomática (McCabe, 1976):** Métrica que quantifica o número de caminhos linearmente independentes através do código. Valores altos indicam maior dificuldade de teste e manutenção, além de potencial maior custo computacional em sistemas com recursos limitados.
* **Refatoração (Fowler, 1999):** Processo de reestruturação de código que melhora sua estrutura interna sem alterar seu comportamento externo. Exemplos: *Extract Method*, *Simplify Conditional Expression*, *Remove Duplication*.
* **Mining Software Repositories (MSR):** Área que utiliza dados históricos de sistemas de controle de versão para extrair conhecimento sobre evolução, práticas e qualidade de software.
* **Sistemas de Tempo Real:** A previsibilidade e frequência de operação (*Loop Rate*) são críticas. Em ECUs, degradação de performance pode resultar em falhas de sincronização com eventos do motor (ex: posição do virabrequim a 8000 RPM = 133 eventos/segundo).

---

## 3. Objetivos e questões (Goal / Question / Metric)

### 3.1 Objetivo geral (Goal template)
**Analisar** o histórico de commits do repositório Speeduino,
**com o propósito de** avaliar o impacto de refatorações documentadas na evolução da complexidade de código e desempenho temporal,
**com relação a** Complexidade Ciclomática e frequência de operação (*Loop Rate*),
**do ponto de vista do** Engenheiro de Software e Pesquisador de Evolução de Software,
**no contexto de** sistemas embarcados automotivos de tempo real crítico desenvolvidos de forma colaborativa (2017-2024).

### 3.2 Objetivos específicos
* **O1:** Identificar e catalogar commits de refatoração no histórico do projeto através de mineração de repositório.
* **O2:** Quantificar o impacto de refatorações na Complexidade Ciclomática das funções afetadas (antes vs. depois).
* **O3:** Medir o impacto de refatorações no desempenho temporal (*Loop Rate*) do firmware.
* **O4:** Classificar refatorações segundo tipos canônicos (Fowler) e identificar quais têm maior impacto em complexidade e performance.
* **O5:** Avaliar se a frequência de refatorações ao longo do tempo está correlacionada com a manutenção ou degradação de desempenho.

### 3.3 Questões de pesquisa / de negócio
* **QP1:** Qual é a frequência e distribuição temporal de commits de refatoração ao longo da evolução do projeto Speeduino?
* **QP2:** Refatorações documentadas reduziram significativamente a Complexidade Ciclomática (CCN) das funções afetadas?
* **QP3:** Existe correlação estatisticamente significativa entre commits de refatoração e melhoria de desempenho (*Loop Rate*)?
* **QP4:** Quais tipos de refatoração (segundo catálogo de Fowler) apresentam maior impacto em complexidade e performance?
* **QP5:** A ausência ou baixa frequência de refatorações em determinados períodos está associada à degradação de desempenho observada nas versões modernas?

### 3.4 Métricas associadas (GQM)

| ID | Questão | Métrica | Definição | Unidade | Fonte |
|:---|:--------|:--------|:----------|:--------|:------|
| **M1** | QP1 | Frequência de Refatorações | Número de commits de refatoração por ano | commits/ano | Git log |
| **M2** | QP1 | Taxa de Refatoração | Percentual de commits de refatoração sobre total de commits | % | Git log |
| **M3** | QP2 | Delta CCN (Δ CCN) | Diferença de Complexidade Ciclomática antes e depois da refatoração | Score (int) | Lizard |
| **M4** | QP2 | Taxa de Redução CCN | Percentual de redução de CCN após refatoração | % | Calculado |
| **M5** | QP3 | Delta Loop Rate (Δ Hz) | Diferença de Loop Rate antes e depois da refatoração | Hz | TunerStudio |
| **M6** | QP3 | Taxa de Melhoria Performance | Percentual de melhoria de Loop Rate após refatoração | % | Calculado |
| **M7** | QP4 | Tipo de Refatoração | Classificação segundo catálogo de Fowler | Categórica | Análise manual |
| **M8** | QP5 | Intervalo entre Refatorações | Tempo médio entre refatorações consecutivas | Dias | Git log |

---

## 4. Escopo e contexto do experimento

### 4.1 Escopo funcional / de processo (incluído e excluído)

**Incluído:**
* Análise de commits do repositório oficial do Speeduino no GitHub (branch `main`/`master`) entre 2017 e 2024.
* Identificação de commits com palavras-chave de refatoração: "refactor", "refactoring", "cleanup", "simplify", "optimize", "restructure".
* Seleção de 10-15 refatorações significativas (que alteraram >30 linhas de código em arquivos críticos).
* Compilação e medição de pares "antes/depois" para cada refatoração selecionada.
* Classificação de refatorações segundo tipos canônicos (Extract Method, Simplify Conditional, Remove Duplication, etc.).
* Hardware de teste: Arduino Mega 2560 (target principal do projeto).

**Excluído:**
* Commits que apenas adicionam novas funcionalidades (não são refatorações).
* Refatorações em código de documentação, scripts de build ou arquivos de configuração.
* Análise de outros hardwares (STM32, Teensy) ou firmwares concorrentes (RusEFI, Megasquirt).
* Testes com motor real em funcionamento (apenas testes em bancada, estado Idle).
* Análise de cobertura de testes (o projeto pode não ter testes unitários estruturados).

### 4.2 Contexto do estudo
O estudo é classificado como **pesquisa observacional retrospectiva** utilizando **mineração de repositório de software**. O contexto é acadêmico-laboratorial, analisando um projeto open-source automotivo maduro (7+ anos) desenvolvido por uma comunidade global distribuída. A criticidade é alta, pois o software controla segurança veicular (ignição e injeção). O pesquisador atua como observador externo sem influência no histórico do projeto.

### 4.3 Premissas
1. Assume-se que commits com mensagens contendo palavras-chave de refatoração representam genuinamente refatorações estruturais (não apenas renomeações cosméticas).
2. Assume-se que versões históricas do código são compiláveis com toolchains modernas ou através de ambientes containerizados (Docker).
3. Assume-se que o método de cálculo de *Loop Rate* interno do Speeduino manteve-se consistente ao longo das versões analisadas.
4. Assume-se que diferenças no compilador `avr-gcc` ao longo do tempo não invalidam comparações (usa-se a mesma versão para todas as compilações).

### 4.4 Restrições
* **Hardware:** Um único Arduino Mega 2560 disponível para testes físicos.
* **Tempo:** Coleta de dados e análise devem ser concluídas em 3 semanas.
* **Orçamento:** Zero. Uso exclusivo de ferramentas gratuitas/open-source.
* **Acesso:** Dependência de disponibilidade pública do repositório GitHub (se tornar-se privado, análise é inviabilizada).

### 4.5 Limitações previstas
* **Validade Externa:** Resultados aplicam-se especificamente a sistemas embarcados AVR 8-bit. Generalizações para arquiteturas ARM 32-bit ou outros domínios devem ser cautelosas.
* **Validade de Construção:** A classificação de tipos de refatoração é subjetiva e depende da interpretação do pesquisador. Utilizar-se-á validação por amostragem com segundo avaliador.
* **Causalidade:** Correlação entre refatoração e performance não implica necessariamente causalidade direta (outros fatores podem ter mudado simultaneamente).
* **Seleção de Commits:** Possível viés de seleção ao escolher apenas refatorações "significativas". Documentar-se-á explicitamente os critérios de seleção.

---

## 5. Stakeholders e impacto esperado

### 5.1 Stakeholders principais
* **Pesquisador (Aluno):** Executor do estudo e interessado direto na validação da proposta de TCC.
* **Comunidade de Desenvolvedores Speeduino:** Mantenedores e contribuidores do projeto open-source.
* **Usuários do Speeduino:** Proprietários de veículos modificados que utilizam o firmware em hardware legado (Arduino Mega).
* **Academia:** Pesquisadores da área de Evolução de Software e Mining Software Repositories.

### 5.2 Interesses e expectativas dos stakeholders
* **Pesquisador:** Validar se refatorações em sistemas embarcados críticos têm impacto mensurável em desempenho; obter dados para TCC e possível publicação científica.
* **Desenvolvedores:** Receber feedback quantitativo sobre o impacto de suas refatorações; identificar práticas que funcionam ou não para performance.
* **Usuários:** Compreender se devem atualizar para versões recentes ou manter versões antigas por questões de performance; receber recomendações baseadas em evidências.
* **Academia:** Ampliar conhecimento sobre a efetividade de refatorações em contextos de sistemas críticos com recursos limitados.

### 5.3 Impactos potenciais no processo / produto
* **Processo de Desenvolvimento:** Resultados podem influenciar políticas de code review no projeto (ex: exigir medição de impacto em performance para refatorações em código crítico).
* **Produto:** Identificação de refatorações específicas que degradaram performance pode levar a reversões ou re-refatorações.
* **Documentação:** Possível criação de guia de "Boas Práticas de Refatoração para Sistemas Embarcados" na Wiki do projeto.
* **Decisões de Hardware:** Se degradação for irreversível, pode justificar descontinuação oficial do suporte ao Arduino Mega.

---

## 6. Riscos de alto nível, premissas e critérios de sucesso

### 6.1 Riscos de alto nível
* **Risco Técnico (Alto):** Versões históricas podem não compilar devido a mudanças em bibliotecas Arduino ou dependências externas. **Mitigação:** Uso de containers Docker com ambientes de build da época; consulta a documentação histórica do projeto.
* **Risco de Dados (Médio):** Commits de refatoração podem estar mal documentados (mensagens genéricas como "fix") ou não identificáveis. **Mitigação:** Análise manual de diffs de uma amostra de commits para validar palavras-chave.
* **Risco de Interpretação (Médio):** Classificação de tipos de refatoração é subjetiva. **Mitigação:** Uso de dois avaliadores independentes para 30% da amostra; cálculo de concordância (Cohen's Kappa).
* **Risco de Instrumentação (Baixo):** TunerStudio pode não conectar com versões muito antigas. **Mitigação:** Fallback para leitura direta via Serial Monitor com script Python.

### 6.2 Critérios de sucesso globais (go / no-go)
O experimento será considerado bem-sucedido se:
* **Critério 1:** Identificar e analisar pelo menos **10 pares antes/depois** de refatorações válidas.
* **Critério 2:** Conseguir compilar e medir *Loop Rate* de pelo menos **80% dos pares** selecionados.
* **Critério 3:** Obter dados de CCN e Loop Rate com variância controlada (desvio padrão < 10% nas medições repetidas).
* **Critério 4:** Responder pelo menos **3 das 5 questões de pesquisa** com evidências estatísticas (p < 0.05 ou análise descritiva robusta).

### 6.3 Critérios de parada antecipada
O experimento deve ser cancelado ou pivotado se:
* **Condição 1:** Menos de 50% das versões históricas conseguirem compilar após tentativas com Docker/toolchains antigas.
* **Condição 2:** Descobrir que o *Loop Rate* é artificialmente limitado por software (delay fixo) em todas as versões, invalidando a variável dependente.
* **Condição 3:** Menos de 5 refatorações válidas forem identificadas no histórico (inviabiliza análise estatística).
* **Condição 4:** O repositório tornar-se privado ou inacessível durante o período de coleta.

---

## 7. Modelo conceitual e hipóteses

### 7.1 Modelo conceitual do experimento
O modelo baseia-se na teoria de que **refatorações bem executadas** reduzem a complexidade estrutural do código (medida por CCN) e, em sistemas com recursos limitados, essa simplificação pode resultar em **redução de ciclos de CPU** necessários para executar a mesma lógica, aumentando assim a frequência de operação (*Loop Rate*).

**Modelo Causal Proposto:**
```
Refatoração → ↓ CCN → ↓ Ciclos de CPU → ↑ Loop Rate
```

**Fatores Mediadores:**
* **Tipo de Refatoração:** Nem todas as refatorações focam em performance (algumas visam legibilidade).
* **Otimização do Compilador:** O compilador pode compensar ou amplificar os efeitos da refatoração.
* **Contexto da Função:** Refatorações em funções no "caminho crítico" (chamadas a cada loop) têm maior impacto que em funções auxiliares.

### 7.2 Hipóteses formais (H0, H1)

**Para QP2 (Impacto em Complexidade):**
* **H0:** Não existe diferença estatisticamente significativa na Complexidade Ciclomática média antes e depois de refatorações (μ_CCN_antes = μ_CCN_depois).
* **H1:** Refatorações reduzem significativamente a Complexidade Ciclomática (μ_CCN_antes > μ_CCN_depois).

**Para QP3 (Impacto em Performance):**
* **H0:** Não existe diferença estatisticamente significativa no *Loop Rate* antes e depois de refatorações (μ_Hz_antes = μ_Hz_depois).
* **H1:** Refatorações aumentam significativamente o *Loop Rate* (μ_Hz_antes < μ_Hz_depois).

**Para QP4 (Tipos de Refatoração):**
* **H0:** Não existe diferença no impacto de performance entre diferentes tipos de refatoração.
* **H1:** Refatorações do tipo "Simplify Conditional" e "Extract Method" têm impacto maior em performance que refatorações de "Rename" ou "Move".

### 7.3 Nível de significância e considerações de poder
* **Nível de Significância (α):** 0,05 (5%). Rejeitaremos H0 se p < 0,05.
* **Poder Estatístico:** Com 10-15 pares antes/depois e medições repetidas (n=10 por versão), espera-se poder estatístico moderado (β ≈ 0.70-0.80) para detectar diferenças de efeito médio (Cohen's d ≥ 0.5).
* **Justificativa:** Dado o contexto exploratório e restrições de tempo, priorizamos viabilidade sobre poder máximo. Resultados serão interpretados com cautela quanto a erros Tipo II.

---

## 8. Variáveis, fatores, tratamentos e objetos de estudo

### 8.1 Objetos de estudo
Os objetos de estudo são **commits de refatoração** e as **versões de código** (snapshots) imediatamente antes e depois desses commits. Cada objeto inclui:
* Código-fonte completo do firmware Speeduino.
* Binário compilado (.hex) para Arduino Mega 2560.
* Metadados do commit (hash, data, autor, mensagem).

### 8.2 Sujeitos / participantes (visão geral)
Este é um estudo **sem participantes humanos diretos**. Os "sujeitos" são artefatos de software. O pesquisador atua como observador e analista. Não há questões éticas relacionadas a sujeitos humanos, mas considera-se o aspecto ético de atribuir corretamente autoria e respeitar licenças open-source.

### 8.3 Variáveis independentes (fatores) e seus níveis
* **Fator Principal:** **Evento de Refatoração**
    * **Níveis:** Antes da Refatoração (baseline) vs. Depois da Refatoração (intervenção)
* **Fator Secundário (para análise estratificada):** **Tipo de Refatoração**
    * **Níveis:** Extract Method, Simplify Conditional, Remove Duplication, Inline, Rename, Move, Outros (segundo Fowler, 1999)

### 8.4 Tratamentos (condições experimentais)
Cada par de medições (antes/depois) representa uma "unidade experimental":
* **Condição Controle:** Versão do código imediatamente **antes** do commit de refatoração.
* **Condição Tratamento:** Versão do código imediatamente **depois** do commit de refatoração.

**Exemplo:**
* Commit `a3f4b2c` (2023-05-12): "Refactor fuel calculation to reduce complexity"
    * Controle: `a3f4b2c~1` (commit pai)
    * Tratamento: `a3f4b2c`

### 8.5 Variáveis dependentes (respostas)
* **VD1 - Complexidade Ciclomática (CCN):**
    * Média, máxima e total de CCN das funções afetadas pela refatoração.
    * Unidade: Score (inteiro)
* **VD2 - Loop Rate (Hz):**
    * Frequência de execução do laço principal em estado Idle.
    * Unidade: Hertz (Hz)
    * Medição: Média de 10 amostras de 60 segundos cada.
* **VD3 - Tamanho do Binário (Bytes):**
    * Tamanho do arquivo .hex compilado (memória Flash ocupada).
    * Unidade: Bytes

### 8.6 Variáveis de controle / bloqueio
Para garantir que diferenças observadas sejam atribuíveis à refatoração:
* **Hardware:** Mesmo Arduino Mega 2560 em todos os testes.
* **Compilador:** Mesma versão de `avr-gcc` e Arduino IDE para todas as compilações.
* **Configuração de Otimização:** Flags de compilação mantidas constantes (`-Os` - otimização de tamanho).
* **Tune (Calibração):** Mesmo arquivo `base_tune.msq` carregado em todas as medições.
* **Ambiente de Teste:** Temperatura controlada (~24°C), mesma fonte de alimentação USB.
* **Protocolo de Medição:** Mesmo tempo de estabilização (30s) e duração de coleta (60s) para Loop Rate.

### 8.7 Possíveis variáveis de confusão conhecidas
* **Mudanças Simultâneas:** Um commit de refatoração pode incluir pequenas correções de bugs ou ajustes que não são puramente estruturais. **Controle:** Análise manual de diffs para validar que o commit é predominantemente refatoração.
* **Evolução do Compilador:** Se commits analisados abrangem grande período temporal, versões diferentes do compilador podem ter sido usadas originalmente. **Controle:** Recompilação de todas as versões com o mesmo compilador moderno.
* **Estado da EEPROM:** Configurações persistentes podem afetar comportamento. **Controle:** Limpeza de EEPROM entre testes (`EEPROM.clear()`).
* **Flutuações de Clock:** Variações de temperatura podem afetar o oscilador de 16MHz. **Controle:** Medições repetidas e descarte de outliers.

---

## 9. Desenho experimental

### 9.1 Tipo de desenho
**Desenho de Medidas Repetidas Pareadas (Paired Repeated Measures)** com análise retrospectiva. Cada par antes/depois é uma unidade experimental independente. Não há randomização de tratamento (a ordem temporal é fixa), mas a **ordem de execução das medições** será randomizada para evitar efeitos de aprendizagem ou aquecimento do hardware.

### 9.2 Randomização e alocação
* **Randomização de Ordem de Execução:** A sequência de medição dos pares antes/depois será randomizada (ex: medir Par 5, depois Par 2, depois Par 8) para evitar que fatores ambientais progressivos (aquecimento, degradação do hardware) favoreçam sistematicamente uma condição.
* **Alocação:** Não aplicável (estudo observacional, não há alocação de sujeitos a tratamentos).

### 9.3 Balanceamento e contrabalanço
* **Balanceamento:** Cada refatoração terá exatamente 2 medições (antes e depois), com o mesmo número de repetições (n=10) para garantir comparabilidade.
* **Contrabalanço:** Para cada par, a ordem de teste (antes vs. depois) será alternada (ex: Par 1 = antes→depois, Par 2 = depois→antes) para neutralizar efeitos de ordem.

### 9.4 Número de grupos e sessões
* **Número de Pares (Unidades Experimentais):** 10-15 refatorações selecionadas.
* **Repetições por Condição:** 10 medições de Loop Rate para cada versão (antes e depois).
* **Total de Medições:** 
    * 12 pares × 2 condições × 10 repetições = **240 medições de Loop Rate**.
    * 12 pares × 2 condições × 1 análise estática = **24 análises de CCN**.

---

## 10. População, sujeitos e amostragem

### 10.1 População-alvo
A população-alvo são **todos os commits de refatoração** realizados no repositório oficial do Speeduino entre 2017 e 2024 que afetaram código de lógica de controle (não apenas comentários ou formatação).

### 10.2 Critérios de inclusão de sujeitos (Commits)
* Commit contém na mensagem pelo menos uma das palavras-chave: "refactor", "refactoring", "cleanup", "simplify", "optimize", "restructure".
* Commit altera pelo menos 30 linhas de código em arquivos `.ino`, `.cpp` ou `.h` da pasta `speeduino/`.
* Commit modifica funções no "caminho crítico" (ex: `loop()`, `decoders.ino`, `timers.ino`, `auxiliaries.ino`).
* Commit é parte da branch principal (`main` ou `master`).
* Versões antes e depois são compiláveis para Arduino Mega 2560.

### 10.3 Critérios de exclusão de sujeitos
* Commits que apenas adicionam novas funcionalidades sem reestruturação.
* Commits que modificam apenas comentários, espaços em branco ou formatação.
* Commits que alteram exclusivamente arquivos de configuração, build scripts ou documentação.
* Commits em branches experimentais ou que foram posteriormente revertidos.
* Versões que não compilam mesmo após tentativas de ajuste de ambiente.

### 10.4 Tamanho da amostra planejado
* **Meta:** Selecionar **12 pares antes/depois** de refatorações válidas.
* **Justificativa:** 
    * Baseado em revisão preliminar do repositório, estima-se 30-40 commits candidatos.
    * Após aplicação de critérios de inclusão/exclusão e testes de compilação, espera-se viabilidade de 12-15 pares.
    * Tamanho suficiente para análise descritiva robusta e testes estatísticos com poder moderado.

### 10.5 Método de seleção / recrutamento
**Amostragem em Dois Estágios:**

**Estágio 1 - Mineração Automatizada:**
```bash
git log --all --grep="refactor\|cleanup\|simplify\|optimize" \
  --since="2017-01-01" --until="2024-12-31" \
  --pretty=format:"%H|%ai|%an|%s" > refactoring_candidates.csv
```

**Estágio 2 - Seleção Manual:**
1. Revisar manualmente cada commit candidato.
2. Verificar diff para confirmar que é refatoração estrutural significativa.
3. Classificar tipo de refatoração (segundo Fowler).
4. Testar compilação das versões antes/depois.
5. Selecionar os 12 melhores candidatos (priorizando diversidade de tipos e distribuição temporal).

### 10.6 Treinamento e preparação
* **Preparação do Ambiente:**
    * Instalação de Git, Arduino IDE 2.3.2, Lizard, TunerStudio.
    * Configuração de Docker com imagens de toolchains AVR antigas para versões de 2017-2018.
    * Preparação de scripts Python para automação de coleta de dados.
* **Treinamento:**
    * Estudo do catálogo de refatorações de Fowler (1999).
    * Familiarização com estrutura do código Speeduino (revisão de documentação da Wiki).
    * Teste piloto com 2 refatorações para validar protocolo de medição.

---

## 11. Instrumentação e protocolo operacional

### 11.1 Instrumentos de coleta
1. **Git:** Sistema de controle de versão para checkout de commits específicos.
2. **Lizard (Python):** Ferramenta de análise estática para cálculo de CCN.
   ```bash
   lizard -l cpp speeduino/ --csv > metrics_before.csv
   ```
3. **Arduino IDE / arduino-cli:** Para compilação de versões históricas.
4. **TunerStudio MS:** Para conexão via Serial e captura de Loop Rate em tempo real.
5. **Script Python de Coleta:** Para automação de leitura de Serial e cálculo de estatísticas.
6. **Planilha Google Sheets:** Para tabulação centralizada de todos os dados coletados.

### 11.2 Materiais de suporte
* **Checklist de Execução:** Roteiro passo-a-passo para cada par de medição.
* **Matriz de Classificação de Refatorações:** Tabela com tipos de Fowler e exemplos para padronização.
* **Base Tune Padrão:** Arquivo `base_tune.msq` versionado para garantir consistência.
* **Script de Limpeza de EEPROM:** Sketch Arduino para resetar memória persistente.
* **Registro de Observações:** Documento para anotar anomalias, dificuldades de compilação e insights qualitativos.

### 11.3 Procedimento experimental (protocolo – visão passo a passo)

**Para cada par de refatoração selecionado:**

**Fase 1: Preparação (10 min)**
1. Identificar hash do commit de refatoração (`COMMIT_HASH`).
2. Anotar data, autor e mensagem do commit.
3. Criar diretório de trabalho: `experiment/pair_XX/`.

**Fase 2: Medição "ANTES" (40 min)**
4. Checkout da versão anterior: `git checkout COMMIT_HASH~1`
5. Executar Lizard: `lizard speeduino/ --csv > pair_XX_before_ccn.csv`
6. Compilar para Mega2560 via Arduino IDE.
7. Anotar tamanho do binário (Flash/SRAM) exibido no log de compilação.
8. Upload do firmware para o Arduino.
9. Limpar EEPROM: `Tools -> Serial Monitor -> enviar comando "E"`
10. Conectar TunerStudio, carregar `base_tune.msq`.
11. Aguardar 30 segundos de estabilização.
12. Iniciar datalog de 60 segundos, observar coluna "LoopPerSecond".
13. Repetir passos 11-12 por 10 vezes (10 amostras).
14. Calcular média e desvio padrão do Loop Rate.

**Fase 3: Medição "DEPOIS" (40 min)**
15. Checkout da versão refatorada: `git checkout COMMIT_HASH`
16. Repetir passos 5-14.

**Fase 4: Análise Manual (15 min)**
17. Revisar diff: `git diff COMMIT_HASH~1 COMMIT_HASH`
18. Classificar tipo de refatoração (Extract Method, Simplify Conditional, etc.).
19. Identificar funções afetadas e documentar.
20. Registrar quaisquer observações qualitativas (ex: "refatoração também corrigiu bug sutil").

**Fase 5: Registro de Dados**
21. Inserir todos os dados na planilha mestre:
    * Hash, data, autor, tipo de refatoração.
    * CCN antes/depois (média, máxima, total).
    * Loop Rate antes/depois (média, desvio padrão, outliers).
    * Tamanho binário antes/depois.

**Tempo total por par:** ~2 horas (incluindo compilação e medições).

### 11.4 Plano de piloto
**Escopo do Piloto:**
* Selecionar 2 refatorações conhecidas (uma "Extract Method" e uma "Simplify Conditional").
* Executar o protocolo completo para ambas.
* Validar:
    * Se o tempo estimado (2h/par) é realista.
    * Se as medições de Loop Rate têm variância aceitável (CV < 5%).
    * Se Lizard consegue parsear o código corretamente.
    * Se TunerStudio conecta com versões antigas.

**Critérios de Ajuste:**
* Se CV > 10% no Loop Rate, aumentar número de repetições de 10 para 15.
* Se TunerStudio não conectar, implementar leitura via Serial Monitor com regex.
* Se tempo por par > 3h, reduzir número de pares planejados de 12 para 10.

---

## 12. Plano de análise de dados (pré-execução)

### 12.1 Estratégia geral de análise
Os dados serão analisados em 3 fases:

**Fase 1 - Análise Descritiva:**
* Frequência e distribuição temporal de refatorações (QP1).
* Estatísticas descritivas de CCN e Loop Rate antes/depois (médias, medianas, ranges).
* Visualizações: linha do tempo de refatorações, boxplots de CCN e Hz, gráficos de dispersão.

**Fase 2 - Análise Inferencial:**
* Testes pareados para avaliar significância das diferenças antes/depois (QP2, QP3).
* Análise estratificada por tipo de refatoração (QP4).
* Correlação entre magnitude de redução de CCN e ganho de performance (QP3).

**Fase 3 - Análise Qualitativa:**
* Revisão de commits com maior impacto positivo ou negativo.
* Identificação de padrões contextuais (ex: refatorações em funções de interrupção têm maior impacto).

### 12.2 Métodos estatísticos planejados
1. **Teste t de Student Pareado:** Para comparar CCN e Loop Rate antes vs. depois (se dados forem normais).
   * Hipótese: μ_antes ≠ μ_depois
   * α = 0.05
2. **Teste de Wilcoxon Signed-Rank:** Alternativa não-paramétrica se dados violarem normalidade.
3. **Teste de Shapiro-Wilk:** Para verificar normalidade das diferenças antes/depois.
4. **ANOVA One-Way:** Para comparar impacto de diferentes tipos de refatoração.
5. **Correlação de Spearman:** Entre Δ CCN e Δ Loop Rate.
6. **Tamanho de Efeito (Cohen's d):** Para quantificar magnitude das diferenças observadas.

### 12.3 Tratamento de dados faltantes e outliers
* **Dados Faltantes:** 
    * Se uma versão não compilar, tentar versão imediatamente anterior/posterior no histórico.
    * Se persistir, excluir o par e documentar a razão.
* **Outliers:** 
    * Identificar via método IQR (1.5 × IQR).
    * Valores de Loop Rate = 0 ou > 500 Hz serão considerados erros de medição e descartados.
    * Se > 20% das medições de um par forem outliers, remedir o par completo.
    * Manter registro de outliers removidos para transparência.

### 12.4 Plano de análise para dados qualitativos
* **Mensagens de Commit:** Análise de conteúdo para identificar motivações declaradas das refatorações (performance vs. legibilidade vs. correção de bugs).
* **Observações do Pesquisador:** Categorização de dificuldades encontradas (ex: "compilação falhou devido a biblioteca deprecated") para discussão de limitações.
* **Padrões de Código:** Extração de snippets de código antes/depois das refatorações mais impactantes para ilustrar mudanças estruturais específicas.

---

## 13. Avaliação de validade (ameaças e mitigação)

### 13.1 Validade de conclusão
**Ameaças:**
* Baixo poder estatístico devido a tamanho amostral limitado (n=10-12 pares).
* Variabilidade alta nas medições de Loop Rate devido a interferências ambientais.
* Violação de suposições de testes paramétricos (normalidade, homogeneidade de variância).

**Mitigações:**
* Realizar múltiplas medições (10 repetições) para aumentar confiabilidade.
* Usar testes não-paramétricos (Wilcoxon) como alternativa robusta.
* Reportar tamanhos de efeito além de valores-p.
* Interpretar resultados com cautela, reconhecendo limitações de poder.

### 13.2 Validade interna
**Ameaças:**
* **History:** Mudanças no hardware ou ambiente entre medições de pares diferentes.
* **Instrumentação:** Variações no método de medição ou calibração de ferramentas.
* **Selection:** Viés na seleção de refatorações (escolher apenas as "bem-sucedidas").
* **Ambiguidade de Direção Causal:** Outros commits próximos podem confundir atribuição de efeito.

**Mitigações:**
* Randomizar ordem de execução dos pares para distribuir efeitos de history.
* Usar mesmo hardware, mesma versão de ferramentas, protocolo padronizado.
* Documentar critérios de seleção de forma transparente; incluir refatorações "neutras" se identificadas.
* Analisar commits adjacentes para verificar se há mudanças não relacionadas à refatoração.

### 13.3 Validade de constructo
**Ameaças:**
* **CCN como proxy de complexidade:** CCN mede apenas complexidade de fluxo de controle, não considera complexidade cognitiva ou dataflow.
* **Loop Rate como proxy de performance:** Medição em Idle pode não representar comportamento sob carga real (motor em funcionamento).
* **Classificação subjetiva de tipos de refatoração:** Interpretação do pesquisador pode não ser consensual.

**Mitigações:**
* Complementar CCN com métricas adicionais (NLOC, profundidade de aninhamento).
* Reconhecer explicitamente que Loop Rate Idle é aproximação; discutir implicações para cenários reais.
* Usar validação por segundo avaliador para 30% da classificação de refatorações; calcular concordância (Kappa).

### 13.4 Validade externa
**Ameaças:**
* Resultados específicos para arquitetura AVR 8-bit podem não generalizar para ARM 32-bit.
* Projeto open-source automotivo pode ter padrões de refatoração diferentes de projetos comerciais ou outros domínios.
* Hardware antigo (Arduino Mega) pode ter limitações não presentes em MCUs modernos.

**Mitigações:**
* Delimitar claramente o escopo de generalização no relatório.
* Comparar resultados com literatura sobre refatoração em outros contextos (quando disponível).
* Discutir explicitamente diferenças entre sistemas embarcados e aplicações desktop/servidor.

### 13.5 Resumo das principais ameaças e estratégias de mitigação

| Ameaça | Tipo | Severidade | Mitigação |
|:-------|:-----|:-----------|:----------|
| Baixo poder estatístico | Conclusão | Média | 10 repetições por condição; uso de tamanhos de efeito |
| Variabilidade de medições | Conclusão | Média | Ambiente controlado; outlier detection |
| Mudanças simultâneas em commits | Interna | Alta | Análise manual de diffs; commits adjacentes |
| Viés de seleção | Interna | Média | Critérios explícitos; documentação transparente |
| CCN não captura toda complexidade | Constructo | Baixa | Métricas complementares; discussão de limitações |
| Idle não representa carga real | Constructo | Alta | Reconhecimento explícito; recomendações para estudos futuros |
| Generalização limitada a AVR | Externa | Alta | Escopo bem delimitado; comparação com literatura |

---

## 14. Ética, privacidade e conformidade

### 14.1 Questões éticas
* **Uso de Código Open-Source:** O código do Speeduino está sob licença GPL-3.0, que permite uso, modificação e redistribuição. O estudo respeita os termos da licença.
* **Atribuição de Autoria:** Todos os autores de commits analisados serão anonimizados na publicação (referenciados apenas por IDs numéricos, não por nomes reais), a menos que seja publicação científica que exija citação nominal (nesse caso, será solicitada permissão).
* **Impacto na Comunidade:** Resultados negativos sobre refatorações específicas serão comunicados de forma construtiva, focando em aprendizados, não em críticas pessoais a desenvolvedores.

### 14.2 Consentimento informado
* **Não Aplicável:** O estudo analisa artefatos públicos (commits em repositório público) sem interação direta com participantes humanos. Não há coleta de dados pessoais além dos já públicos no histórico do Git.
* **Transparência:** Após conclusão do estudo, resultados serão compartilhados com a comunidade Speeduino via Issue no GitHub ou post no fórum oficial, permitindo discussão e feedback.

### 14.3 Privacidade e proteção de dados
* **Dados Públicos:** Todos os dados analisados (commits, código-fonte) são públicos por natureza do repositório open-source.
* **Anonimização:** Nomes de desenvolvedores serão substituídos por IDs alfanuméricos (ex: "Dev_A", "Dev_B") em publicações acadêmicas para proteger privacidade, a menos que autores deem consentimento explícito para citação nominal.
* **Armazenamento de Dados:** Dados coletados (CSVs, logs) serão armazenados em repositório privado do pesquisador, com backup em Google Drive institucional, pelo período de 5 anos (conforme regulamento da instituição).
* **Compartilhamento:** Datasets anonimizados poderão ser disponibilizados publicamente em plataformas como Zenodo/Figshare para replicabilidade, mediante aprovação do orientador.

---

**Fim do Plano de Experimento v1.0**

**Assinatura Digital:**
Luiz Filipe Nery Costa
PUC Minas - Engenharia de Software
02/12/2025
