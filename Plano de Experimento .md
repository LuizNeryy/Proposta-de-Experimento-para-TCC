# Plano de Experimento – Scoping e Planejamento

## 1. Identificação básica

### 1.1 Título do experimento
Análise Longitudinal do Impacto da Complexidade Ciclomática e Consumo de Recursos no Desempenho Temporal (Loop Rate) de Firmware Automotivo (Speeduino).

### 1.2 ID / código
`EXP-MES-2025-SPEEDUINO-01`

### 1.3 Versão do documento e histórico de revisão
* **Versão:** v2.0 (Ajuste para Entrega 2)
* **Histórico:**
    * 19/11/2025: v1.0 - Criação do documento inicial.
    * 25/11/2025: v2.0 - Expansão do GQM, inclusão de tabelas de métricas e refinamento de escopo conforme requisitos da Entrega 2.

### 1.4 Datas (criação, última atualização)
* **Criação:** 19/11/2025
* **Última atualização:** 25/11/2025

### 1.5 Autores (nome, área, contato)
* **Autor:** Luiz Filipe Nery Costa
* **Área:** Engenharia de Software
* **Contato:** lfncosta@sga.pucminas.br

### 1.6 Responsável principal (PI / dono do experimento)
* **Aluno Responsável:** Luiz Filipe Nery Costa
* **Professor Supervisor:** Prof. Danilo

### 1.7 Projeto / produto / iniciativa relacionada
Este experimento integra a avaliação da disciplina de **Medição e Experimentação de Software**. O estudo é uma Prova de Conceito (*PoC*) para TCC, focado na análise de *Software Bloat* em Sistemas Embarcados Automotivos Críticos, utilizando o firmware *Speeduino* como objeto de estudo.

---

## 2. Contexto e problema

### 2.1 Descrição do problema / oportunidade
Sistemas de injeção eletrônica (ECUs) operam em *hard real-time*. O projeto *Speeduino* cresceu significativamente em funcionalidades (Turbo, VVT, CAN Bus). O problema investigado é se esse crescimento (aumento de complexidade e tamanho) degradou a frequência de operação (*Loop Rate*) em hardwares limitados (Arduino Mega), a ponto de comprometer a segurança ou viabilidade técnica em motores de alta rotação.

### 2.2 Contexto organizacional e técnico
* **Ambiente:** Acadêmico/Laboratorial (Simulação HIL - Hardware-in-the-Loop).
* **Domínio:** Sistemas Embarcados Automotivos.
* **Hardware:** ATmega2560 (Arduino Mega R3).
* **Ferramentas:**
    * *Análise Estática:* Lizard (Python) e Arduino Compiler (avr-gcc).
    * *Dinâmica:* TunerStudio MS (Telemetria Serial).

### 2.3 Trabalhos e evidências prévias
* **Literatura:** As Leis de Lehman (evolução de software aumenta complexidade se não mitigada).
* **Comunidade:** Relatos empíricos de que versões pós-2022 são "mais lentas" para conectar ou manter sincronismo em altas RPMs comparadas a versões de 2017.

### 2.4 Referencial teórico e empírico essencial
* **Complexidade Ciclomática (McCabe):** Medida de caminhos independentes no código.
* **Sistemas de Tempo Real:** Importância do *Loop Frequency* (Hz) para amostragem de sensores (Teorema de Nyquist).
* **Recursos Embarcados:** Impacto do uso de SRAM (Heap/Stack) na latência de execução.

---

## 3. Objetivos e questões (GQM)

### 3.1 Objetivo Geral
**Analisar** o firmware Speeduino (código-fonte e binário), **com o propósito de** avaliar a correlação entre evolução da complexidade, consumo de memória e desempenho temporal, **com relação a** eficiência de processamento (*Loop Rate*) e manutenibilidade, **do ponto de vista do** Engenheiro de Software, **no contexto de** microcontroladores AVR 8-bit executando versões de 2017 a 2024.

### 3.2, 3.3 e 3.4 Matriz GQM (Objetivos Específicos, Questões e Métricas)

| Objetivo Específico | Questões de Pesquisa (Questions) | Métricas Associadas (Metrics) |
| :--- | :--- | :--- |
| **O1.** Quantificar a evolução estrutural e de tamanho do firmware ao longo das releases. | **Q1.1** O tamanho físico do projeto (linhas de código e arquivos) cresceu de forma linear ou exponencial? | • M1: Total NLOC<br>• M2: File Count |
| | **Q1.2** A complexidade lógica média das funções aumentou ou se manteve estável com as novas funcionalidades? | • M3: Average CCN<br>• M4: Function Count |
| | **Q1.3** Existem "hotspots" de complexidade que surgiram nas versões mais recentes (funções gigantes)? | • M5: Max CCN<br>• M3: Average CCN |
| **O2.** Medir o consumo de recursos de hardware (Memória e Processamento) em cada versão. | **Q2.1** Qual é a frequência de operação basal (*Idle Loop Rate*) de cada versão no hardware de referência? | • M6: Loop Rate (Hz)<br>• M7: Delta Performance (%) |
| | **Q2.2** O binário compilado está se aproximando do limite físico da memória Flash do microcontrolador? | • M8: Flash Usage (Bytes)<br>• M9: Flash Usage (%) |
| | **Q2.3** O consumo de memória RAM dinâmica (variáveis globais) aumentou significativamente? | • M10: SRAM Usage (Bytes)<br>• M11: SRAM Usage (%) |
| **O3.** Correlacionar métricas de código estáticas com o desempenho dinâmico. | **Q3.1** Existe correlação estatística entre o aumento da Complexidade Ciclomática (CCN) e a queda do Loop Rate? | • M3: Average CCN<br>• M6: Loop Rate (Hz) |
| | **Q3.2** O aumento do tamanho do binário (Flash) impacta diretamente na velocidade de execução? | • M8: Flash Usage (Bytes)<br>• M6: Loop Rate (Hz) |
| | **Q3.3** O aumento no número de funções e arquivos impactou a performance geral? | • M4: Function Count<br>• M7: Delta Performance (%) |
| **O4.** Avaliar a viabilidade técnica das versões modernas no hardware legado. | **Q4.1** A versão mais recente ainda mantém um Loop Rate acima do mínimo seguro para 8000 RPM? | • M6: Loop Rate (Hz)<br>• M12: Safe Threshold Margin |
| | **Q4.2** Qual foi a taxa de degradação anual média do desempenho? | • M7: Delta Performance (%)<br>• M13: Years since Baseline |
| | **Q4.3** A manutenibilidade do código (densidade de comentários) piorou conforme o projeto cresceu? | • M14: Comment Density<br>• M1: Total NLOC |

### 3.5 Tabela de Definição de Métricas

| ID | Nome da Métrica | Descrição | Unidade | Fonte de Dados |
|:---|:---|:---|:---|:---|
| **M1** | Total NLOC | Número total de linhas de código (excluindo comentários e vazios). | Linhas (int) | Lizard |
| **M2** | File Count | Número total de arquivos de código fonte (.ino, .cpp, .h) analisados. | Arquivos (int) | Lizard / OS |
| **M3** | Average CCN | Média da Complexidade Ciclomática de todas as funções do projeto. | Score (float) | Lizard |
| **M4** | Function Count | Número total de funções detectadas no projeto. | Funções (int) | Lizard |
| **M5** | Max CCN | O maior valor de Complexidade Ciclomática encontrado em uma única função. | Score (int) | Lizard |
| **M6** | Loop Rate | Frequência de execuções do laço principal por segundo em repouso. | Hz | TunerStudio |
| **M7** | Delta Performance | Variação percentual de desempenho em relação à versão base (2017). | % | Calculado |
| **M8** | Flash Usage (Bytes)| Tamanho do binário compilado ocupando a memória de programa. | Bytes | Compilador (avr-size) |
| **M9** | Flash Usage (%) | Percentual da memória Flash total (256KB) ocupada. | % | Compilador |
| **M10**| SRAM Usage (Bytes)| Quantidade de memória RAM alocada para variáveis globais estáticas. | Bytes | Compilador (avr-size) |
| **M11**| SRAM Usage (%) | Percentual da memória SRAM total (8KB) ocupada. | % | Compilador |
| **M12**| Safe Threshold | Margem de segurança entre o Loop Rate medido e o mínimo teórico (ex: 100Hz). | Hz | Calculado |
| **M13**| Years Time | Tempo decorrido em anos desde a primeira versão analisada. | Anos | Metadados Git |
| **M14**| Comment Density | Razão entre linhas de comentário e linhas totais, indicando documentação. | % | Lizard/Cloc |

---

## 4. Escopo e contexto do experimento

### 4.1 Escopo funcional / de processo
* **Incluído (In-Scope):**
    * Análise de 5 a 6 versões "Major Stable" do Speeduino (ex: 2017, 2019, 2021, 2023, 2024).
    * Compilação direcionada para o target `MEGA2560`.
    * Coleta de métricas estáticas de todo o diretório fonte.
    * Coleta dinâmica apenas em estado "Idle" (Key-On, Engine-Off) para isolar a variável de processamento de código.
* **Excluído (Out-of-Scope):**
    * Testes com motor em funcionamento real (risco de danos).
    * Análise de outras arquiteturas (STM32, Teensy) ou outros firmwares (RusEFI).
    * Análise de consumo de energia elétrica (mA).

### 4.2 Contexto do estudo
* **Organização:** Acadêmica (Laboratório de Engenharia de Software da PUC Minas).
* **Projeto:** Speeduino (Open Source, licença GPL), desenvolvido por comunidade global.
* **Criticidade:** Alta. O software controla injeção e ignição; falhas causam desligamento do motor.
* **Participante:** Aluno de graduação com perfil Sênior em desenvolvimento, atuando como experimentador.

### 4.3 Premissas
1.  O *overhead* de comunicação serial (telemetria) é constante em todas as versões e não distorcerá a comparação relativa do *Loop Rate*.
2.  As versões antigas do código (Legacy) são compatíveis com o compilador `avr-gcc` atual disponível na Arduino IDE 2.x.
3.  O hardware (Arduino Mega) mantém clock estável de 16MHz durante todos os testes (sem *thermal throttling*).

### 4.4 Restrições
* **Tempo:** O experimento deve ser executado e tabulado em 2 semanas.
* **Recursos:** Apenas 1 unidade física de teste disponível.
* **Ferramentas:** Uso exclusivo de ferramentas gratuitas/open-source (Lizard, Arduino IDE, Versão free do TunerStudio).

### 4.5 Limitações previstas
* **Validade Externa:** Os resultados são válidos apenas para a arquitetura AVR de 8-bits. Processadores modernos de 32-bits podem mascarar a complexidade devido ao alto poder de processamento.
* **Validade de Construção:** A métrica CCN mede caminhos lógicos, mas não necessariamente o custo computacional de cada instrução (uma linha complexa matematicamente pode pesar mais que 10 `if`s simples).

---

## 5. Stakeholders e impacto esperado

### 5.1 Stakeholders principais
* **Experimentador (Aluno):** Executor da coleta e análise.
* **Supervisor Acadêmico:** Validador metodológico.
* **Comunidade Speeduino (Users):** Proprietários de carros antigos usando Arduino Mega.
* **Maintainers do Projeto:** Desenvolvedores que aprovam PRs no GitHub.

### 5.2 Interesses e expectativas
* **Aluno:** Validar hipótese de TCC e aprovação na disciplina.
* **Comunidade:** Saber se devem evitar atualizações recentes em hardware antigo ("Devo ficar na versão 2020?").
* **Maintainers:** Identificar necessidade de otimização ou decisão de descontinuar suporte oficial ao Atmega2560.

### 5.3 Impactos potenciais
* **Produto:** Possível recomendação oficial na Wiki do projeto sobre "Requisitos Mínimos de Versão".
* **Processo:** Introdução de *checks* automatizados de complexidade no CI/CD do projeto original (sugestão futura).

---

## 6. Riscos de alto nível, premissas e critérios de sucesso

### 6.1 Riscos de alto nível
* **Técnico (Toolchain):** Falha na compilação de versões >5 anos devido a dependências de bibliotecas externas que mudaram.
* **Dados (Inconsistência):** O *Loop Rate* oscilar demais (Jitter alto), dificultando a definição de uma média confiável.
* **Ambiente:** Queima da porta Serial/USB do Arduino devido a conexões/desconexões frequentes.

### 6.2 Critérios de sucesso globais
* Obtenção de dados completos (Métricas Estáticas + Dinâmicas) para no mínimo **4 versões** diferentes.
* Identificação de uma tendência clara (seja ela correlação positiva, negativa ou nula) com significância estatística visível em gráficos de dispersão.

### 6.3 Critérios de parada antecipada
* Se as versões antigas não compilarem mesmo após 3 tentativas de ajuste de ambiente (Docker/VM).
* Se o *Loop Rate* for fixo (travado via software) em todas as versões, eliminando a variável dependente.