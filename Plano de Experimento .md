# Plano de Experimento – Scoping e Planejamento

## 1. Identificação básica

### 1.1 Título do experimento
Análise Longitudinal do Impacto da Complexidade Ciclomática no Desempenho Temporal (Loop Rate) de Firmware Automotivo Open-Source (Speeduino).

### 1.2 ID / código
`EXP-MES-2025-SPEEDUINO-01`

### 1.3 Versão do documento e histórico de revisão
* **Versão:** v1.0
* **Histórico:**
    * 19/11/2025: Criação do documento inicial para a disciplina de Medição e Experimentação.

### 1.4 Datas (criação, última atualização)
* **Criação:** 19/11/2025
* **Última atualização:** 19/11/2025

### 1.5 Autores (nome, área, contato)
* **Autor:** Luiz Filipe Nery Costa
* **Área:** Engenharia de Software
* **Contato:** lfncosta@sga.pucminas.br

### 1.6 Responsável principal (PI / dono do experimento)
* **Aluno Responsável:** Luiz Filipe Nery Costa
* **Professor Supervisor:** Prof. Danilo

### 1.7 Projeto / produto / iniciativa relacionada
Este experimento é parte integrante da avaliação da disciplina de **Medição e Experimentação de Software**. O estudo serve como prova de conceito (*Proof of Concept*) para a definição de tema de Trabalho de Conclusão de Curso (TCC) focado na interseção entre Engenharia de Software e Sistemas Embarcados Automotivos. O objeto de estudo é o firmware *Speeduino*.

---

## 2. Contexto e problema

### 2.1 Descrição do problema / oportunidade
Sistemas de injeção eletrônica (ECUs) operam em regime de tempo real crítico (*hard real-time*), onde o atraso no processamento pode resultar em perda de eficiência do motor. O projeto *Speeduino* tem sofrido um crescimento constante de funcionalidades ao longo dos últimos anos (adição de controle de turbo, VVT, CAN Bus, etc.).
O problema a ser investigado é o fenômeno de "Software Bloat" em microcontroladores limitados: busca-se entender se o aumento da complexidade do código ao longo das versões resultou em uma degradação significativa da frequência de operação (*Loop Rate*), o que poderia inviabilizar o uso de versões modernas do software em hardwares de entrada ou em motores de alta rotação.

### 2.2 Contexto organizacional e técnico
* **Ambiente:** O experimento será executado em ambiente acadêmico/laboratorial.
* **Domínio:** Sistemas Embarcados Automotivos (*Automotive Embedded Systems*).
* **Metodologia de Teste:** Simulação HIL (*Hardware-in-the-Loop*) simplificada de baixo custo.
* **Hardware:** Microcontrolador ATmega2560 (Arduino Mega).
* **Ferramentas de Software:**
    * **Coleta de Métricas de Código:** Lizard (Python) e/ou CppCheck.
    * **Compilação:** Arduino IDE / PlatformIO.
    * **Monitoramento:** TunerStudio (software de calibração de ECUs).

### 2.3 Trabalhos e evidências prévias (internos e externos)
* **Literatura Externa:** As Leis de Lehman sobre evolução de software indicam que a complexidade tende a crescer e a qualidade tende a declinar se não houver esforço de refatoração.
* **Evidências da Comunidade:** Relatos em fóruns de desenvolvedores do Speeduino sugerem que versões mais recentes do firmware apresentam *Loop Rates* inferiores em comparação a versões "Legacy" quando executadas no mesmo hardware, embora falte uma análise quantitativa formal correlacionando isso à métrica de código.

### 2.4 Referencial teórico e empírico essencial
* **Complexidade Ciclomática (McCabe):** Métrica fundamental para quantificar a complexidade do fluxo de controle do software.
* **Real-Time Systems Performance:** Conceitos de latência, *jitter* e frequência de amostragem (*Loop Rate*) em sistemas de controle em malha fechada.
* **Technical Debt (Dívida Técnica):** O conceito de que o acúmulo de código complexo e não refatorado "cobra juros" na forma de degradação de performance ou manutenibilidade.

## 3. Objetivos e questões (Goal / Question / Metric)

### 3.1 Objetivo geral (Goal template)
**Analisar** o firmware de injeção eletrônica Speeduino (código-fonte em C++ e binário compilado),
**com o propósito de** avaliar a correlação entre a evolução da complexidade interna do código e a degradação do desempenho temporal,
**com relação a** sua eficiência de processamento em tempo real (Loop Rate) e manutenibilidade (Complexidade Ciclomática),
**do ponto de vista do** Engenheiro de Software e Integrador de Sistemas Embarcados,
**no contexto de** microcontroladores de arquitetura limitada (Arduino Mega 2560 / AVR 8-bit) executando versões lançadas entre 2017 e 2024.

### 3.2 Objetivos específicos
* **O1:** Quantificar a evolução da complexidade ciclomática média e total do firmware ao longo das principais *releases* estáveis.
* **O2:** Medir a frequência de operação principal (*Main Loop Rate*) em Hertz para cada versão selecionada, mantendo o hardware constante.
* **O3:** Identificar estatisticamente se existe uma correlação negativa forte entre o aumento da complexidade (CCN) e a queda da frequência de operação (Hz).
* **O4:** Verificar se versões recentes do firmware ainda atendem aos requisitos mínimos de operação para motores de alta rotação no hardware original (Mega 2560).

### 3.3 Questões de pesquisa / de negócio
* **QP1:** Qual foi a taxa de crescimento da Complexidade Ciclomática (CCN) do projeto Speeduino nos últimos 7 anos?
* **QP2:** Qual é o impacto quantitativo (percentual de queda) no *Loop Rate* ao comparar a versão mais antiga selecionada com a versão estável mais recente?
* **QP3:** Existe uma correlação linear observável entre o aumento da métrica de complexidade do código e a redução do desempenho em tempo real?
* **QP4:** O *overhead* introduzido por novas funcionalidades ("bloat") tornou o Arduino Mega 2560 obsoleto para aplicações de alta performance neste projeto?

### 3.4 Métricas associadas (GQM)

| ID | Questão | Nome da Métrica | Definição | Unidade | Fonte de Dados | Ferramenta |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **M1** | QP1, QP3 | **Average CCN** | Média da Complexidade Ciclomática de McCabe de todas as funções analisadas. | Número Inteiro (Score) | Código Fonte (Static Analysis) | Lizard (Python) |
| **M2** | QP1 | **NLOC Total** | Número de linhas de código não vazias e sem comentários (tamanho físico). | Linhas | Código Fonte | Lizard / CLOC |
| **M3** | QP2, QP3 | **Loop Rate (Idle)** | Frequência de execução do laço principal (`void loop()`) com a ECU ligada, sem rotação (0 RPM), apenas sensores básicos. | Hz (Hertz) | Telemetria via Serial | TunerStudio MS |
| **M4** | QP4 | **Delta Performance** | Diferença percentual de Hz entre a Versão $V_{i}$ e a Versão Base $V_{0}$. | % (Percentual) | Cálculo Derivado | Planilha de Análise |

---

## 4. Escopo e contexto do experimento

### 4.1 Escopo funcional / de processo (incluído e excluído)
* **Incluído:**
    * Análise das versões "Official Stable Releases" do Speeduino (amostragem anual).
    * Compilação para o target `MEGA2560`.
    * Configuração "Base Tune" (padrão do Speeduino) carregada em todas as versões para garantir paridade de processamento.
    * Medição em bancada (sem motor real) utilizando estimuladores de sinal se necessário, ou estado de repouso (Key-On, Engine-Off).
* **Excluído:**
    * Análise de hardwares mais potentes (Teensy, STM32), pois mascarariam o problema de performance.
    * Testes em dinamômetro ou veículos reais (risco físico e custo elevado).
    * Análise de "Code Churn" ou rotatividade de desenvolvedores.
    * Impacto de interferência eletromagnética (ruído) no hardware.

### 4.2 Contexto do estudo
O estudo ocorre em **contexto acadêmico (Laboratório de Engenharia de Software)**, simulando um ambiente de P&D de sistemas embarcados. O projeto analisado é **Open Source (GPL)**, caracterizado por desenvolvimento colaborativo, distribuído e, por vezes, sem controle rígido de requisitos de desempenho ("feature creep"). O nível de experiência do participante é de estudante sênior de graduação.

### 4.3 Premissas
* O hardware Arduino Mega 2560 utilizado está em perfeitas condições elétricas e não sofrerá degradação física durante os testes.
* As versões antigas do firmware (Legacy) ainda são compiláveis com toolchains modernas (avr-gcc atual) ou será possível configurar um ambiente de compilação compatível.
* O protocolo de comunicação Serial para o TunerStudio manteve-se compatível o suficiente para extrair o dado de "Loop Rate" em todas as versões.

### 4.4 Restrições
* **Hardware:** Limitado a uma única unidade física de Arduino Mega 2560 (não haverá testes de variância entre chips).
* **Tempo:** O experimento deve ser concluído dentro do semestre letivo (aprox. 4 semanas para execução e análise).
* **Orçamento:** Custo zero (utilização de equipamentos e licenças já disponíveis).

### 4.5 Limitações previstas (Ameaças à Validade)
* **Validade Externa:** Os resultados são específicos para o firmware Speeduino na arquitetura AVR. Podem não se aplicar a outros firmwares de ECU (ex: RusEFI, Megasquirt) ou arquiteturas ARM.
* **Validade Interna:** A medição em "repouso" ou com estimulador sintético não recria perfeitamente a carga de interrupções de um motor real a 8000 RPM, o que significa que a degradação de performance real pode ser ainda pior do que a medida.

---

## 5. Stakeholders e impacto esperado

### 5.1 Stakeholders principais
* **Pesquisador:** Luiz Filipe Nery Costa (Aluno).
* **Supervisor:** Prof. Danilo (Orientador acadêmico).
* **Comunidade Speeduino:** Desenvolvedores e usuários do projeto open-source (interessados em saber se devem ou não atualizar o firmware em hardwares antigos).
* **Desenvolvedores de Sistemas Embarcados:** Interessados na relação teórica entre métricas de código e performance em tempo real.

### 5.2 Interesses e expectativas dos stakeholders
* **Academia:** Validar a aplicação prática de métricas de software (Lizard/McCabe) em um cenário de Hardware-in-the-Loop.
* **Comunidade:** Receber uma recomendação técnica ("Use a versão X para o Arduino Mega, evite a versão Y se tiver motor de alta performance").
* **Pesquisador:** Confirmar a hipótese de pesquisa para estruturação do TCC.

### 5.3 Impactos potenciais no processo / produto
* O experimento não altera o código-fonte do projeto oficial, portanto, não há risco de regressão no produto.
* Os resultados podem gerar **Issues/Pull Requests** no repositório oficial sugerindo refatorações ou atualização da documentação de requisitos mínimos de hardware.

---

## 6. Riscos de alto nível, premissas e critérios de sucesso

### 6.1 Riscos de alto nível
* **Risco Técnico (Alto):** Incompatibilidade de compilação ("Bit rot"). Versões muito antigas do código podem depender de bibliotecas do Arduino IDE que foram descontinuadas ou alteradas, impedindo a geração do binário.
    * *Mitigação:* Usar versões "Portable" da Arduino IDE antigas ou Docker containers para builds legados.
* **Risco de Ferramental (Médio):** O Lizard pode não conseguir parsear corretamente macros complexas de C++ usadas em sistemas embarcados, gerando métricas imprecisas.
    * *Mitigação:* Validação manual por amostragem de 2 ou 3 arquivos principais (`speeduino.ino`, `decoders.ino`).
* **Risco de Dados (Baixo):** O TunerStudio não conectar com versões antigas devido a mudanças na assinatura do protocolo serial.
    * *Mitigação:* Utilizar versões "Legacy" do TunerStudio se necessário.

### 6.2 Critérios de sucesso globais (go / no-go)
* **Sucesso:** Conseguir compilar, carregar e medir dados de pelo menos **5 versões distintas** (distribuídas uniformemente ao longo do tempo) e obter métricas de código para as mesmas.
* **Viabilidade da Análise:** Os dados coletados devem apresentar variância suficiente para plotar um gráfico de dispersão (não pode ser uma linha reta constante).

### 6.3 Critérios de parada antecipada
* Impossibilidade de compilar mais de 50% das versões selecionadas devido a erros de toolchain.
* Falha do hardware (Arduino).
* Verificação de que o "Loop Rate" é travado via software (hard-coded cap) em todas as versões, o que invalidaria a variável dependente do experimento.