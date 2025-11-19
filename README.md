# Análise de Complexidade vs. Performance no Speeduino 🏎️💻

Este repositório contém os artefatos de planejamento, execução e análise de dados para o experimento da disciplina de **Medição e Experimentação de Software**.

## 📌 Sobre o Projeto

O objetivo deste estudo é realizar uma análise longitudinal no firmware open-source de injeção eletrônica **[Speeduino](https://github.com/speeduino/speeduino)**. 

Investigamos a correlação entre o aumento da **Complexidade Ciclomática** (ocasionada pela evolução e adição de novas funcionalidades ao longo dos anos) e o impacto no **Desempenho Temporal (Loop Rate)** em microcontroladores de recursos limitados (Arduino Mega 2560).

**A principal questão de pesquisa é:** > *"O aumento da complexidade do código ao longo das versões resultou em uma degradação significativa da frequência de operação da ECU?"*

## 🎓 Contexto Acadêmico

* **Curso:** Engenharia de Software
* **Disciplina:** Medição e Experimentação de Software
* **Professor Supervisor:** Prof. Danilo
* **Aluno Responsável:** Luiz Filipe Nery Costa

## 📂 Estrutura do Repositório

* 📄 `plano-experimento.md`: Documento de Scoping e Planejamento inicial.

## 🛠️ Tecnologias e Ferramentas

Este experimento utiliza uma abordagem de **Hardware-in-the-Loop (HIL)** simplificada:

* **Objeto de Estudo:** Firmware Speeduino (C++).
* **Hardware:** Arduino Mega 2560 (Atmel AVR).
* **Análise Estática:** [Lizard](https://github.com/terryyin/lizard) (Python) para medição de Complexidade Ciclomática.
* **Monitoramento:** TunerStudio MS para coleta de Loop Rate (Hz).

---
Developed by **Luiz Filipe Nery Costa** using Open Source tools.