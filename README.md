# Análise do Impacto de Refatorações em Firmware Automotivo 🏎️💻

Este repositório contém os artefatos de planejamento para o experimento da disciplina de **Medição e Experimentação de Software** e fundamentação metodológica para o **Trabalho de Conclusão de Curso (TCC)**.

## 📌 Sobre o Projeto

O objetivo deste estudo é realizar uma **análise retrospectiva de Mining Software Repositories** no firmware open-source de injeção eletrônica **[Speeduino](https://github.com/speeduino/speeduino)**. 

Investigamos o **impacto de refatorações históricas** realizadas ao longo de 7 anos (2017-2024) na **Complexidade Ciclomática** e no **Desempenho Temporal (Loop Rate)** em microcontroladores de recursos limitados (Arduino Mega 2560).

Diferentemente de abordagens experimentais que propõem novas refatorações, este estudo **analisa commits já realizados pela comunidade** para avaliar se práticas de refatoração conseguiram mitigar ou reverter a degradação de performance.

**As principais questões de pesquisa são:**
> - *"Refatorações documentadas reduziram significativamente a Complexidade Ciclomática das funções afetadas?"*
> - *"Existe correlação entre commits de refatoração e melhoria de performance (Loop Rate)?"*
> - *"Quais tipos de refatoração tiveram maior impacto em complexidade e performance?"*

## 🎓 Contexto Acadêmico

* **Curso:** Engenharia de Software
* **Disciplina:** Medição e Experimentação de Software
* **Professor Supervisor:** Prof. Danilo
* **Aluno Responsável:** Luiz Filipe Nery Costa

## 📂 Estrutura do Repositório

* 📄 `Enunciado Geral.md`: Template completo com todas as seções de planejamento experimental.
* 📄 `Plano de Experimento .md`: Versão inicial (v3.0) - Abordagem observacional simples.
* 📄 `Plano de Experimento V2.md`: **Versão atual (v4.0)** - Abordagem de Mining Software Repositories + Análise de Impacto de Refatorações.

## 🔬 Abordagem Metodológica

Este experimento utiliza **Mining Software Repositories (MSR)** combinado com testes em hardware real:

### **Fase 1: Mineração de Repositório**
* Análise de histórico de commits do Speeduino no GitHub (2017-2024)
* Identificação de commits de refatoração via palavras-chave (`refactor`, `simplify`, `optimize`, etc.)
* Seleção de 10-15 refatorações significativas

### **Fase 2: Análise de Impacto (Pares Antes/Depois)**
* **Análise Estática:** [Lizard](https://github.com/terryyin/lizard) para medir Complexidade Ciclomática (CCN)
* **Análise Dinâmica:** Medição de Loop Rate (Hz) via TunerStudio MS
* **Hardware de Teste:** Arduino Mega 2560 (AVR 8-bit)

### **Fase 3: Análise Estatística**
* Testes pareados (t-test / Wilcoxon)
* Correlação entre Δ CCN e Δ Loop Rate
* Classificação de tipos de refatoração (catálogo de Fowler)

## 🛠️ Ferramentas Utilizadas

* **Git:** Mineração de histórico de commits
* **Lizard (Python):** Análise estática de complexidade
* **Arduino IDE / avr-gcc:** Compilação de versões históricas
* **TunerStudio MS:** Telemetria e medição de desempenho em tempo real
* **Python / R:** Análise estatística de dados

---
Developed by **Luiz Filipe Nery Costa** using Open Source tools.