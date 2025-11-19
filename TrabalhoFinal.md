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