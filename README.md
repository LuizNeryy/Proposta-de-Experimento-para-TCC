# Análise Longitudinal da Evolução de Complexidade e Desempenho no Firmware Speeduino 🏎️💻

Este repositório contém os artefatos de planejamento para o experimento da disciplina de **Medição e Experimentação de Software** e fundamentação metodológica para o **Trabalho de Conclusão de Curso (TCC)**.

## 📌 Sobre o Projeto

O objetivo deste estudo é realizar uma **análise longitudinal quantitativa** do firmware open-source de injeção eletrônica **[Speeduino](https://github.com/speeduino/speeduino)** ao longo de **7 anos de evolução** (2017-2024).

Investigamos se o fenômeno conhecido como ***Software Bloat*** (inchaço de software) impacta negativamente o **desempenho temporal** em sistemas de tempo real crítico executando em microcontroladores de recursos limitados (Arduino Mega 2560).

O estudo mede objetivamente a **correlação entre o aumento da Complexidade Ciclomática do código** e a **degradação de performance (Loop Rate)**, determinando se o hardware "legado" ainda é seguro para motores de alta rotação.

**As principais questões de pesquisa são:**
> - *"O aumento da Complexidade Ciclomática média (CCN) do firmware apresenta uma tendência linear de crescimento ao longo das versões?"*
> - *"Existe uma diferença estatisticamente significativa no Loop Rate (Hz) entre as versões antigas (Legacy) e as modernas?"*
> - *"Qual a força da correlação entre o aumento da complexidade total do projeto e a redução da frequência de operação?"*

## 🎓 Contexto Acadêmico

* **Curso:** Engenharia de Software
* **Disciplina:** Medição e Experimentação de Software
* **Professor Supervisor:** Prof. Danilo
* **Aluno Responsável:** Luiz Filipe Nery Costa

## 📂 Estrutura do Repositório

* 📄 `Enunciado Geral.md`: Template completo com todas as 20 seções de planejamento experimental.
* 📄 `Plano de Experimento .md`: **Versão atual (v3.1)** - Análise longitudinal de complexidade e desempenho (Completo com 20 seções + Fluxograma).
* 📄 `Plano de Experimento V2.md`: Versão alternativa (v4.0) - Abordagem MSR de Refatorações (arquivada).

## 🔬 Abordagem Metodológica

Este experimento utiliza um **desenho de um fator (One-Factor Design)** com análise de séries temporais:

### **Fase 1: Seleção de Versões**
* Seleção sistemática de **6 versões estáveis** do Speeduino (2017-2024)
* Critério: primeira release oficial de cada ano
* Compilação de todas as versões para Arduino Mega 2560

### **Fase 2: Medição de Complexidade e Desempenho**
* **Análise Estática:** [Lizard](https://github.com/terryyin/lizard) para medir:
  * Complexidade Ciclomática (CCN)
  * Linhas de Código (NLOC)
  * Tamanho do binário compilado
* **Análise Dinâmica:** Medição de Loop Rate (Hz) via TunerStudio MS
  * 30 repetições de 60 segundos por versão
  * Hardware de teste: Arduino Mega 2560 (ATmega2560, 16MHz)
  * Configuração padrão: 4 cilindros, Base Tune

### **Fase 3: Análise Estatística**
* Teste de normalidade (Shapiro-Wilk)
* Correlação entre CCN e Loop Rate (Pearson/Spearman)
* ANOVA para comparação entre versões
* Regressão linear para previsão de tendências

## 🛠️ Ferramentas Utilizadas

* **Git:** Mineração de histórico de commits
* **Lizard (Python):** Análise estática de complexidade
* **Arduino IDE / avr-gcc:** Compilação de versões históricas
* **TunerStudio MS:** Telemetria e medição de desempenho em tempo real
* **Python / R:** Análise estatística de dados

---
Developed by **Luiz Filipe Nery Costa** using Open Source tools.