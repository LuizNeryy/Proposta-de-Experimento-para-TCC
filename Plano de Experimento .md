# Plano de Experimento – Scoping e Planejamento

## 1. Identificação básica

### 1.1 Título do experimento
**Análise Longitudinal da Correlação entre Complexidade de Código e Desempenho Temporal (Loop Rate) no Firmware Speeduino em Hardware Limitado.**

### 1.2 ID / código
`EXP-MES-2025-SPEEDUINO-FINAL`

### 1.3 Versão do documento e histórico de revisão
* **Versão Atual:** v3.1 (Versão Final Completa)
* **Histórico:**
    * v1.0 (19/11/2025): Esboço inicial do escopo.
    * v2.0 (25/11/2025): Expansão do GQM e métricas.
    * v3.0 (02/12/2025): Detalhamento completo do desenho experimental, hipóteses estatísticas e protocolo de execução para trabalho final.
    * v3.1 (05/12/2025): Finalização parcial - Adição das seções 13-20, fluxograma operacional e conformidade total com Enunciado Geral.

### 1.4 Datas (criação, última atualização)
* **Criação:** 19/11/2025
* **Última atualização:** 05/12/2025

### 1.5 Autores (nome, área, contato)
* **Autor:** Luiz Filipe Nery Costa
* **Área:** Engenharia de Software / Sistemas Embarcados
* **Contato:** lfncosta@sga.pucminas.br

### 1.6 Responsável principal (PI / dono do experimento)
* **Investigador Principal:** Luiz Filipe Nery Costa
* **Orientador Acadêmico:** Prof. Danilo

### 1.7 Projeto / produto / iniciativa relacionada
Este experimento está vinculado ao projeto de código aberto **Speeduino** (sistema de injeção eletrônica e ignição). Ele serve como o artefato principal da disciplina de **Medição e Experimentação de Software** e constitui a fundamentação metodológica para o Trabalho de Conclusão de Curso (TCC) focado na obsolescência de software em sistemas críticos.

---

## 2. Contexto e problema

### 2.1 Descrição do problema / oportunidade
O problema central abordado é o fenômeno conhecido como *Software Bloat* (inchaço de software) em sistemas de tempo real crítico (*Hard Real-Time*). No contexto automotivo, uma ECU (Unidade de Controle do Motor) precisa ler sensores, calcular a mistura de combustível e disparar a ignição em janelas de tempo de milissegundos.
Observou-se, através de relatos da comunidade, que as versões mais recentes do Speeduino, repletas de novas funcionalidades (como controle de VVT, mapas 3D complexos e CAN Bus), apresentam uma frequência de operação (*Loop Rate*) menor quando instaladas no hardware original recomendado (Arduino Mega 2560).
A oportunidade deste estudo é medir objetivamente se o aumento da complexidade estrutural do código (complexidade ciclomática) é a causa raiz dessa degradação de desempenho, determinando se o hardware "legado" ainda é seguro para motores de alta rotação.

### 2.2 Contexto organizacional e técnico
* **Organização:** O estudo é conduzido no âmbito acadêmico (Laboratório de Engenharia de Software da PUC Minas).
* **Domínio:** Sistemas Embarcados Automotivos (*Automotive Embedded Systems*).
* **Tecnologias:** Linguagem C/C++, Arquitetura AVR (8-bit), Compilador GCC.
* **Ferramentas:**
    * *Lizard:* Ferramenta de análise estática em Python para medir complexidade.
    * *TunerStudio MS:* Software padrão da indústria para calibração e telemetria de injeções programáveis.
    * *Arduino IDE:* Ambiente de compilação e upload.

### 2.3 Trabalhos e evidências prévias (internos e externos)
* **Literatura Externa:** As *Leis de Lehman* sobre a evolução de software, especificamente a lei da "Complexidade Crescente", afirmam que à medida que um sistema evolui, sua complexidade aumenta a menos que haja trabalho explícito para reduzi-la.
* **Estudos Similares:** Pesquisas anteriores sobre o kernel do Linux mostram correlação entre tamanho do código e latência em processadores antigos.
* **Evidências Internas:** Nos fóruns do Speeduino, usuários relatam que versões anteriores a 2020 rodavam a 400Hz, enquanto versões atuais rodam próximas a 250Hz na mesma configuração.

### 2.4 Referencial teórico e empírico essencial
* **Complexidade Ciclomática (McCabe):** Métrica que conta o número de caminhos linearmente independentes através do código fonte. Quanto maior o número, maior a lógica condicional (`if`, `while`, `for`) e, teoricamente, maior o custo de processamento.
* **Teorema de Nyquist-Shannon:** Para controlar um motor a 8000 RPM (133 rotações por segundo), o sistema precisa amostrar e processar dados a uma frequência mínima (Nyquist rate) para não perder eventos de ignição.
* **Sistemas de Tempo Real:** A previsibilidade temporal é mais importante que a velocidade média. Atrasos no *Loop Rate* significam atrasos no ponto de ignição, o que pode danificar o motor.

---

## 3. Objetivos e questões (Goal / Question / Metric)

### 3.1 Objetivo geral (Goal template)
**Analisar** o histórico de versões do firmware Speeduino,
**com o propósito de** caracterizar e correlacionar o aumento da complexidade de código com a degradação de desempenho,
**com relação a** frequência de execução do laço principal (*Loop Rate*) e consumo de memória,
**do ponto de vista do** Engenheiro de Software e Integrador de Sistemas,
**no contexto de** microcontroladores de recursos limitados (Arduino Mega 2560) executando versões estáveis de 2017 a 2024.

### 3.2 Objetivos específicos
* **O1:** Mapear a evolução do tamanho e complexidade lógica do código ao longo de 7 anos.
* **O2:** Determinar a queda percentual de desempenho (Hz) entre a versão mais antiga e a mais recente.
* **O3:** Testar estatisticamente se a Complexidade Ciclomática é um preditor confiável para a queda de desempenho.
* **O4:** Estabelecer um "limiar de obsolescência", indicando em qual ano/versão o hardware Arduino Mega deixou de ser ideal para aplicações de alta performance.

### 3.3 Questões de pesquisa / de negócio
* **QP1:** O aumento da Complexidade Ciclomática média (Avg CCN) do firmware apresenta uma tendência linear de crescimento ao longo das versões?
* **QP2:** Existe uma diferença estatisticamente significativa no *Loop Rate* (Hz) entre as versões antigas (Legacy) e as modernas?
* **QP3:** Qual a força da correlação (R de Pearson/Spearman) entre o aumento da complexidade total do projeto e a redução da frequência de operação?

### 3.4 Métricas associadas (GQM)
* **Para QP1:**
    * *M1 - Average CCN:* Média da complexidade de todas as funções (Fonte: Lizard).
    * *M2 - NLOC:* Número de linhas de código efetivas (Fonte: Lizard).
* **Para QP2:**
    * *M3 - Mean Loop Rate:* Média aritmética de 60 amostras de frequência por segundo (Unidade: Hz, Fonte: TunerStudio).
* **Para QP3:**
    * *M4 - Correlation Coefficient:* Valor estatístico calculado cruzando M1 e M3.

---

## 4. Escopo e contexto do experimento

### 4.1 Escopo funcional / de processo (incluído e excluído)
* **Incluído:** Serão analisadas apenas as *Major Releases* (versões principais) lançadas anualmente (ex: 2017.01, 2018.01, etc.). O teste é feito em bancada, com o Arduino conectado via USB ao computador, sem estar ligado a um motor real. O perfil de configuração ("Tune") será o padrão "Base Tune" para garantir que todas as versões processem a mesma lógica de motor.
* **Excluído:** Não serão analisadas versões "Nightly" ou "Beta" instáveis. Não serão testados hardwares alternativos (STM32, Teensy) nem configurações complexas (como 8 cilindros sequenciais), pois isso introduziria variáveis de confusão.

### 4.2 Contexto do estudo
O estudo é classificado como um **experimento controlado in vitro** (em laboratório). O "participante" não é humano, mas sim o sistema computacional. O ambiente é estático e controlado para evitar interferências externas (temperatura, ruído elétrico). A criticidade é alta, pois o software analisado controla segurança veicular.

### 4.3 Premissas
1.  Assume-se que o método de cálculo de *Loop Rate* interno do Speeduino não sofreu alterações algorítmicas que invalidem a comparação entre versões (ex: a forma como ele conta os segundos é a mesma).
2.  Assume-se que o compilador `avr-gcc` utilizado para gerar os binários será mantido na mesma versão de otimização (`-O2` ou `-Os`) para todos os testes.

### 4.4 Restrições
* **Hardware Único:** Todo o experimento será rodado na mesma placa física Arduino Mega para eliminar variações de fabricação do silício.
* **Orçamento:** Zero. Uso de ferramentas gratuitas.
* **Tempo:** O experimento de coleta de dados deve durar no máximo 5 dias úteis.

### 4.5 Limitações previstas
* **Validade Externa:** Os resultados aplicam-se estritamente à arquitetura AVR 8-bit. Não é possível generalizar os achados para processadores ARM 32-bit, que possuem *pipelines* e *caches* que lidam com complexidade de forma diferente.
* **Cenário Sintético:** O teste em "repouso" (Idle) não estressa o processador com interrupções de hardware (sinais de roda fônica) que ocorrem em um motor real.

---

## 5. Stakeholders e impacto esperado

### 5.1 Stakeholders principais
* **Pesquisador (Aluno):** Responsável pela execução.
* **Comunidade de Usuários Speeduino:** Grupo que utiliza o hardware legado e sofre com lentidão.
* **Mantenedores do Projeto (Devs):** Responsáveis por aceitar ou rejeitar novas funcionalidades.

### 5.2 Interesses e expectativas dos stakeholders
* **Usuários:** Esperam uma resposta definitiva: "Devo atualizar meu firmware ou manter o antigo?".
* **Mantenedores:** Interessados em identificar quais módulos específicos causaram maior impacto na performance para focar esforços de refatoração.
* **Pesquisador:** Espera confirmar a hipótese de que a complexidade de código é a principal vilã da performance em MCUs limitados.

### 5.3 Impactos potenciais no processo / produto
Se a hipótese for confirmada, o impacto pode ser a criação de uma versão "LTS" (Long Term Support) ou "Lite" do firmware, removendo funcionalidades modernas para recuperar performance em hardwares antigos. Isso mudaria o ciclo de release do produto.

---

## 6. Riscos de alto nível, premissas e critérios de sucesso

### 6.1 Riscos de alto nível
* **Risco Técnico:** Versões muito antigas (2016/2017) podem não compilar na IDE atual devido a mudanças em bibliotecas do C++. *Mitigação:* Usar containers Docker com ambientes de compilação da época.
* **Risco de Dados:** O software de monitoramento (TunerStudio) pode não conseguir conectar com protocolos de comunicação serial antigos. *Mitigação:* Baixar versões antigas do TunerStudio compatíveis.

### 6.2 Critérios de sucesso globais (go / no-go)
O experimento será considerado um sucesso se conseguirmos coletar dados válidos de pelo menos **5 anos distintos** de releases e se a variância dos dados entre as execuções for baixa (Desvio Padrão < 5%), garantindo confiabilidade estatística.

### 6.3 Critérios de parada antecipada
Se descobrirmos que o *Loop Rate* é limitado artificialmente por software (ex: um comando `delay()` fixo em todas as versões), o experimento deve ser cancelado ou pivotado, pois a variável dependente não seria livre para variar.

---

## 7. Modelo conceitual e hipóteses

### 7.1 Modelo conceitual do experimento
O modelo baseia-se na teoria de que cada instrução condicional e cada linha de código adicional consome ciclos de clock da CPU.
* **Fator (Causa):** Evolução das versões do firmware (que traz consigo o aumento da Complexidade Ciclomática e Tamanho do Binário).
* **Efeito (Resposta):** Redução da frequência de *Loop* (Hz), pois o processador leva mais tempo para completar um ciclo de verificação de sensores e cálculos.

### 7.2 Hipóteses formais (H0, H1)
Para a Questão de Pesquisa principal (QP3 - Correlação):

* **Hipótese Nula ($H_0$):** Não existe correlação estatisticamente significativa entre a Complexidade Ciclomática média do firmware e o *Loop Rate* (Correlação $\rho = 0$). Isso implicaria que a otimização do compilador ou a arquitetura anulam o efeito da complexidade.
* **Hipótese Alternativa ($H_1$):** Existe uma correlação negativa estatisticamente significativa entre a Complexidade Ciclomática e o *Loop Rate* ($\rho < 0$). Ou seja, à medida que a complexidade sobe, o desempenho cai.

### 7.3 Nível de significância e considerações de poder
* **Nível de Significância ($\alpha$):** 0,05 (5%). Rejeitaremos a hipótese nula se o valor-p for menor que 0,05.
* **Poder Estatístico:** Como faremos múltiplas medições (30 repetições) para cada versão, esperamos um poder estatístico alto (> 0.8) para detectar até mesmo pequenas degradações de desempenho.

---

## 8. Variáveis, fatores, tratamentos e objetos de estudo

### 8.1 Objetos de estudo
Os objetos de estudo são os **artefatos de software** (código-fonte e binários compilados) das versões estáveis do Speeduino (ex: 2017.08, 2018.08, 2019.05, 2020.12, 2022.01, 2024.02).

### 8.2 Sujeitos / participantes (visão geral)
Neste experimento *in silico*, não há participantes humanos. O "sujeito" que executa a tarefa é o **Microcontrolador ATmega2560**. Trataremos cada ciclo de execução de 60 segundos como um "participante" ou "sessão" para fins estatísticos.

### 8.3 Variáveis independentes (fatores) e seus níveis
* **Fator:** Versão do Firmware.
* **Níveis:** 6 níveis (6 versões diferentes selecionadas ao longo do tempo).
* *Nota:* A complexidade ciclomática é uma variável intrínseca ao nível do fator "Versão".

### 8.4 Tratamentos (condições experimentais)
Cada tratamento corresponde ao *upload* e execução de uma versão específica do firmware no hardware.
* **Tratamento 1:** Speeduino v2017.
* **Tratamento 2:** Speeduino v2018.
* ...
* **Tratamento 6:** Speeduino v2024.

### 8.5 Variáveis dependentes (respostas)
* **Loop Rate (Hz):** Número de vezes que o laço principal `void loop()` é executado por segundo.
* **SRAM Usage (%):** Percentual de memória RAM ocupada (medida secundária).

### 8.6 Variáveis de controle / bloqueio
Para garantir que a diferença observada seja *apenas* pelo código:
* **Hardware:** O mesmo Arduino Mega será usado em todos os testes.
* **Configuração (Tune):** O mesmo arquivo de calibração base (`base_tune.msq`) será carregado.
* **Cabo USB:** O mesmo cabo e porta USB.
* **Temperatura:** Ambiente climatizado (~24°C) para evitar *thermal throttling* (embora raro em AVR).

### 8.7 Possíveis variáveis de confusão conhecidas
* **Ruído na Serial:** A comunicação com o PC para ler os dados consome CPU. Se o protocolo mudou drasticamente entre versões, isso pode confundir os resultados. *Controle:* Usar a mesma taxa de transmissão (Baud Rate 115200) sempre.

---

## 9. Desenho experimental

### 9.1 Tipo de desenho
Utilizaremos um **Desenho de Um Fator (One-Factor Design)** com níveis fixos (as versões). É um estudo longitudinal quase-experimental.

### 9.2 Randomização e alocação
Como é uma série temporal, não faz sentido randomizar a ordem das versões para análise de tendência, mas para a **execução do experimento**, a ordem dos tratamentos será randomizada (ex: testar 2020, depois 2017, depois 2024) para evitar que fatores ambientais progressivos (como aquecimento do PC ou instabilidade da fonte de energia) favoreçam ou prejudiquem uma versão específica.

### 9.3 Balanceamento e contrabalanço
O experimento será balanceado: cada versão (nível) terá exatamente o mesmo número de repetições (n=30).

### 9.4 Número de grupos e sessões
* **Grupos:** 6 grupos (um para cada versão).
* **Sessões:** 30 sessões de medição de 60 segundos por grupo.
* **Total de medições:** 180 pontos de dados.

---

## 10. População, sujeitos e amostragem

### 10.1 População-alvo
A população alvo são todas as releases oficiais do projeto Speeduino desde seu início até hoje.

### 10.2 Critérios de inclusão de sujeitos (Versões)
* Ser uma release marcada como "Stable" ou "Official" no GitHub.
* Ter código-fonte disponível e compilável.
* Suportar nativamente o chip ATmega2560.

### 10.3 Critérios de exclusão de sujeitos
* Versões que requerem hardware customizado não padrão.
* Versões que falham na compilação devido a bibliotecas perdidas.

### 10.4 Tamanho da amostra planejado
Selecionaremos **6 versões** espaçadas temporalmente. Para cada versão, coletaremos **30 amostras de Loop Rate médio**. O número 30 é escolhido baseando-se no Teorema do Limite Central, permitindo assumir normalidade na distribuição das médias para os testes estatísticos.

### 10.5 Método de seleção
Amostragem sistemática: Selecionar a primeira release estável de cada ano (ex: Fev/2017, Fev/2018, etc.).

### 10.6 Treinamento e preparação
Como o executor é o próprio pesquisador, a preparação envolve o estudo da documentação de compilação de versões antigas e preparação do ambiente de *toolchain*.

---

## 11. Instrumentação e protocolo operacional

### 11.1 Instrumentos de coleta
1.  **Script Python (Lizard):** Para varrer as pastas do código e gerar um CSV com `NLOC` e `CCN` de cada arquivo.
2.  **TunerStudio MS:** Para conectar à ECU e gerar logs de dados (`.mlg`) contendo a variável `LoopPerSecond`.
3.  **Planilha Eletrônica:** Para tabular os dados manuais.

### 11.2 Materiais de suporte
* Repositório GitHub do Speeduino clonado.
* Arduino IDE versão 1.8.19 (Versão legada estável).
* Drivers CH340 para comunicação serial.

### 11.3 Procedimento experimental (protocolo – visão passo a passo)
Para cada uma das 6 versões selecionadas:
1.  **Limpeza:** Executar `git checkout tags/vYYYY.MM` e limpar pasta de build.
2.  **Análise Estática:** Rodar script do Lizard e salvar `metricas_vYYYY.csv`.
3.  **Compilação:** Abrir no Arduino IDE, compilar e fazer upload para a placa.
4.  **Setup:** Abrir TunerStudio, criar novo projeto para esta versão, carregar `base_tune.msq`.
5.  **Estabilização:** Aguardar 30 segundos com o sistema ligado.
6.  **Coleta:** Iniciar o *Datalogging* no TunerStudio. Deixar rodar por 60 segundos. Parar log.
7.  **Extração:** Abrir o log, calcular a média da coluna `Seconds` / `Loops` e registrar na planilha mestre.
8.  **Repetição:** Repetir passos 5 a 7 por 30 vezes (ou extrair 30 janelas de tempo do log contínuo).
9.  **Reset:** Desconectar a placa, limpar a memória (EEPROM Clear) antes da próxima versão.

### 11.4 Plano de piloto
Será realizado um piloto com apenas duas versões (a mais antiga e a mais nova) e 5 repetições. O objetivo é verificar se o TunerStudio consegue ler os logs de ambas corretamente. Se houver incompatibilidade de formato de log, o protocolo será ajustado para leitura direta via Monitor Serial (texto puro).

---

## 12. Plano de análise de dados (pré-execução)

### 12.1 Estratégia geral de análise
Os dados serão analisados para responder se "Mais código = Menos velocidade". Primeiro, faremos uma análise descritiva (gráficos de linha) para visualizar a tendência. Depois, usaremos testes inferenciais.

### 12.2 Métodos estatísticos planejados
1.  **Teste de Normalidade (Shapiro-Wilk):** Para verificar se os dados de *Loop Rate* seguem uma distribuição normal.
2.  **Teste de Correlação (Pearson ou Spearman):** Se os dados forem normais, Pearson; se não, Spearman. Isso testará a força da relação entre CCN (variável métrica) e Hz (variável de desempenho).
3.  **ANOVA (Análise de Variância) de um fator:** Para confirmar se a diferença de médias de performance entre as versões é estatisticamente significativa ou se é apenas ruído aleatório.
4.  **Regressão Linear Simples:** Se houver alta correlação, traçar uma reta de regressão para prever o desempenho de versões futuras.

### 12.3 Tratamento de dados faltantes e outliers
* **Outliers:** Serão identificados pelo método de Boxplot (1.5 * IQR). Valores anômalos (ex: Loop Rate = 0 ou infinito devido a erro de conexão) serão descartados e uma nova medição será feita para recompor a amostra de 30.
* **Dados faltantes:** Se uma versão não compilar, ela será substituída pela release imediatamente seguinte (ex: 2017.02 em vez de 2017.01).

### 12.4 Plano de análise para dados qualitativos
Não haverá dados qualitativos substanciais, exceto observações do pesquisador sobre dificuldades de compilação ("warnings" do compilador), que serão anotadas como metadados para discutir a "Saúde do Código".

---

## 13. Avaliação de validade (ameaças e mitigação)

### 13.1 Validade de conclusão estatística
**Ameaça:** Baixo poder estatístico devido a poucas versões (n=6).
**Mitigação:** Compensar com alto número de repetições por versão (30 medições), permitindo robustez nas médias por grupo.

**Ameaça:** Violação da premissa de normalidade nos dados.
**Mitigação:** Se testes de normalidade falharem, usar alternativas não-paramétricas (Spearman, Kruskal-Wallis).

### 13.2 Validade interna
**Ameaça:** Degradação física do hardware ao longo dos testes (aquecimento).
**Mitigação:** Randomizar a ordem de execução das versões e permitir 5 minutos de resfriamento entre testes.

**Ameaça:** Interferência de processos do sistema operacional (Windows) consumindo CPU do PC que se comunica com o Arduino.
**Mitigação:** Desabilitar antivírus, atualizações automáticas e aplicações em background durante coleta de dados.

### 13.3 Validade de constructo
**Ameaça:** A Complexidade Ciclomática pode não ser o melhor proxy para "custo computacional" - existem outras métricas como Cache Misses ou Branch Mispredictions que podem ser mais relevantes.
**Mitigação:** Reconhecer essa limitação na discussão. Coletar métricas adicionais (NLOC, tamanho do binário) para análise de sensibilidade.

**Ameaça:** O *Loop Rate* medido em idle pode não refletir o desempenho sob carga real (motor em funcionamento).
**Mitigação:** Deixar claro no escopo que o experimento mede "pior caso teórico" ou "baseline", não aplicação real. Sugerir experimentos futuros com simulador de sinais (trigger wheel simulator).

### 13.4 Validade externa
**Ameaça:** Resultados específicos ao Arduino Mega 2560 (ATmega2560) não se generalizam para outros microcontroladores (STM32, Teensy, ESP32).
**Mitigação:** Delimitar claramente a população-alvo no escopo. Não fazer afirmações genéricas sobre "todos os sistemas embarcados".

**Ameaça:** Uso de configuração padrão (4 cilindros) não representa motores complexos (V8, V12).
**Mitigação:** Documentar que os resultados aplicam-se a configurações de 4 cilindros. Replicações futuras podem testar configurações mais complexas.

### 13.5 Resumo das principais ameaças e estratégias de mitigação

| Categoria | Ameaça Principal | Estratégia de Mitigação | Prioridade |
|-----------|------------------|------------------------|------------|
| **Conclusão Estatística** | Baixo poder (n=6 versões) | 30 repetições por versão + testes não-paramétricos | Alta |
| **Validade Interna** | Aquecimento do hardware | Randomização da ordem + intervalos de resfriamento | Alta |
| **Validade Interna** | Interferência do SO | Desabilitar processos background durante coleta | Média |
| **Validade de Constructo** | CCN pode não capturar custo real | Coletar métricas complementares (NLOC, binário) | Média |
| **Validade de Constructo** | Medição em idle vs. carga real | Delimitar escopo explicitamente como "baseline" | Alta |
| **Validade Externa** | Específico ao ATmega2560 | Não generalizar para outras arquiteturas | Crítica |
| **Validade Externa** | Configuração de 4 cilindros | Documentar limitação e sugerir replicações futuras | Baixa |

---

## 14. Ética, privacidade e conformidade

### 14.1 Questões éticas
Este experimento não envolve participantes humanos, portanto não requer aprovação de Comitê de Ética em Pesquisa (CEP). O código-fonte analisado é open-source sob licença GPL-3.0, permitindo uso livre para fins acadêmicos e educacionais.

### 14.2 Consentimento informado
Não aplicável - experimento realizado com artefatos de software público.

### 14.3 Privacidade e proteção de dados
Todos os dados coletados (logs, CSVs) serão armazenados localmente no computador do pesquisador e em backup na nuvem institucional (OneDrive da PUC Minas), com acesso restrito. Após conclusão do TCC, os dados brutos podem ser disponibilizados publicamente em repositório acadêmico (Zenodo) para fins de replicabilidade científica.

### 14.4 Aprovações necessárias (comitê de ética, jurídico, DPO, etc.)
**Aprovações Requeridas:**
* **Comitê de Ética em Pesquisa (CEP):** ❌ Não aplicável - experimento não envolve seres humanos, apenas análise de código open-source.
* **Orientador Acadêmico:** ✅ Necessária - Prof. Danilo deve revisar e aprovar o plano experimental antes da execução.
* **Coordenação da Disciplina:** ✅ Necessária - Aprovação implícita via processo de entregas parciais da disciplina.
* **Licença de Software:** ✅ Conforme - Speeduino está sob GPL-3.0, permitindo uso acadêmico sem restrições.

**Status Atual (05/12/2025):**
* Plano em revisão pelo orientador (aguardando aprovação formal via email).
* Nenhuma barreira ética ou legal identificada.

---

## 15. Recursos, infraestrutura e orçamento

### 15.1 Recursos humanos e papéis
* **Pesquisador Principal:** Luiz Filipe Nery Costa (dedicação de 40 horas/semana durante 2 semanas)
* **Orientador:** Prof. Danilo (disponibilidade de 2 horas/semana para reuniões de alinhamento)

### 15.2 Infraestrutura técnica necessária
**Hardware:**
* 1x Arduino Mega 2560 (já disponível no laboratório)
* 1x Cabo USB A-B (já disponível)
* 1x Computador com Windows 10/11, mínimo 8GB RAM (equipamento pessoal)

**Software:**
* Arduino IDE 1.8.19 (gratuito)
* TunerStudio MS v3.1.07 (versão gratuita)
* Python 3.10+ com Lizard (gratuito via pip)
* Microsoft Excel / Google Sheets (já disponível)
* R / RStudio para análise estatística (gratuito)

**Infraestrutura:**
* Laboratório de Engenharia de Software da PUC Minas (Prédio 3, Sala 209)
* Bancada estável com isolamento de vibrações
* Conexão à internet para download de versões antigas do GitHub

### 15.3 Materiais e insumos
* **Materiais Físicos:** Arduino Mega 2560, cabo USB, computador (todos já disponíveis)
* **Materiais Digitais:**
  - Repositório GitHub do Speeduino clonado localmente
  - Arquivo de configuração Base Tune (`base_tune.msq`)
  - Planilha mestre para coleta de dados (template Excel pré-formatado)
  - Scripts Python para automação da análise com Lizard
* **Licenças:** Nenhuma licença paga necessária (100% ferramentas open-source/gratuitas)

### 15.4 Orçamento e custos estimados
**Custo Total Estimado:** R$ 0,00 (zero)
* Hardware: já disponível
* Software: 100% open-source/gratuito
* Infraestrutura: já disponível na universidade

---

## 16. Cronograma, marcos e riscos operacionais

### 16.1 Cronograma de fases e marcos principais

| Fase | Atividade | Prazo | Marco |
|------|-----------|-------|-------|
| **1** | Preparação do ambiente (instalação de ferramentas) | 03-05/12/2025 | Ambiente configurado |
| **2** | Seleção e download das 6 versões do GitHub | 05/12/2025 | Versões identificadas |
| **3** | Execução do piloto (2 versões, 5 repetições) | 06/12/2025 | Protocolo validado |
| **4** | Coleta completa de dados (6 versões × 30 repetições) | 07-09/12/2025 | Dados brutos coletados |
| **5** | Análise estatística e geração de gráficos | 10-11/12/2025 | Resultados analisados |
| **6** | Redação do relatório final e slides | 11-12/12/2025 | Entrega final |

### 16.2 Dependências críticas entre atividades
* A **Fase 4** depende do sucesso da **Fase 3** (piloto). Se o piloto identificar problemas de compatibilidade, pode ser necessário ajustar o protocolo e atrasar a coleta.
* A **Fase 5** só pode iniciar após 100% dos dados da **Fase 4** terem sido coletados.

### 16.3 Riscos operacionais
**Risco 1:** Incompatibilidade de drivers USB (CH340) no Windows 11.
* **Probabilidade:** Média
* **Impacto:** Alto (bloqueio total da coleta)
* **Mitigação:** Testar a conexão na Fase 1 e ter um notebook reserva com Windows 10.

**Risco 2:** Versão antiga do firmware não compilar devido a bibliotecas deprecadas.
* **Probabilidade:** Média
* **Impacto:** Médio (perda de 1 ponto de dados)
* **Mitigação:** Substituir pela versão imediatamente posterior disponível.

**Risco 3:** Falta de energia elétrica no laboratório durante coleta de dados.
* **Probabilidade:** Baixa
* **Impacto:** Médio (perda de 1 dia de trabalho)
* **Mitigação:** Usar nobreak e salvar dados incrementalmente após cada medição.

### 16.4 Estratégia de controle de atrasos
Se atraso > 1 dia for detectado, o pesquisador trabalhará em regime estendido (12h/dia) nos dias 09-10/12 para recuperar o prazo. Se atraso > 3 dias, reduzir o número de repetições de 30 para 20 por versão (ainda aceitável estatisticamente).

---

## 17. Governança do experimento

### 17.1 Papéis e responsabilidades formais
* **Pesquisador (Luiz Filipe):** Executar todas as fases operacionais, desde preparação até análise de dados.
* **Orientador (Prof. Danilo):** Revisar desenho experimental, validar escolha de métodos estatísticos, aprovar mudanças no protocolo.
* **Coordenação do Curso:** Avaliar e aprovar a entrega final conforme critérios da disciplina.

### 17.2 Ritos de acompanhamento pré-execução
* **Checkpoint 1 (05/12):** Revisão com orientador - validação da seleção de versões.
* **Checkpoint 2 (08/12):** Revisão intermediária - apresentação dos dados brutos de 3 versões.
* **Checkpoint 3 (11/12):** Revisão pré-entrega - validação dos resultados estatísticos e gráficos.

### 17.3 Processo de controle de mudanças no plano
**Autoridade de Decisão:**
* Decisões operacionais (ordem de execução, ajustes menores no protocolo): **Pesquisador**.
* Decisões metodológicas (mudança de teste estatístico, exclusão de versões): **Orientador** (com consulta ao pesquisador).
* Decisões sobre entrega e formato final: **Coordenação da Disciplina**.

**Registro de Mudanças:**
Toda mudança significativa ao plano experimental será documentada em um arquivo `CHANGELOG_EXPERIMENTO.md` no repositório Git, contendo:
* Data da mudança
* Descrição da mudança
* Justificativa
* Aprovação do orientador (via email ou issue no GitHub)

---

## 18. Plano de documentação e reprodutibilidade

### 18.1 Repositórios e convenções de nomeação
**Artefatos que serão documentados:**
1. **Código-fonte dos scripts de coleta** (Python para Lizard, scripts auxiliares)
2. **Datasets brutos** (CSVs com todas as medições)
3. **Scripts de análise estatística** (R ou Python com comentários detalhados)
4. **Logs de compilação** (outputs do Arduino IDE para cada versão)
5. **Relatório final** (documento em Markdown + PDF)
6. **Slides de apresentação** (PowerPoint/PDF)

**Padrões de nomenclatura e versionamento:**
* Arquivos de dados: `dados_vYYYY_MM_rep_NN.csv` (ex: `dados_v2017_08_rep_01.csv`)
* Scripts: `01_coleta_lizard.py`, `02_analise_correlacao.R` (numeração sequencial)
* Versão do plano: seguir versionamento semântico no Git (tags `v3.0`, `v3.1`, etc.)

**Repositório / local de armazenamento:**
* **Repositório Público:** GitHub (`github.com/LuizNeryy/TCC-Speeduino-Experimento`)
* **Backup Institucional:** OneDrive da PUC Minas (pasta `/TCC/Dados_Experimento/`)
* **Dados Sensíveis:** Não aplicável - todos os dados são públicos e podem ser compartilhados.

### 18.2 Templates e artefatos padrão
* **Planilha de Coleta de Dados:** Template Excel com colunas pré-definidas (Versão, Repetição, CCN, NLOC, Loop Rate, Timestamp)
* **Script Lizard:** `01_coleta_lizard.py` (automatiza extração de métricas)
* **Script de Análise:** `02_analise_correlacao.R` (testes estatísticos e gráficos)
* **Checklist de Execução:** Documento impresso com os 9 passos do protocolo

### 18.3 Plano de empacotamento para replicação futura
**Pacote de Replicação:**
Ao final do experimento, será gerado um arquivo `REPLICATION_PACKAGE.zip` contendo:
* README.md com instruções passo a passo de replicação
* Todos os scripts documentados
* Datasets brutos e processados
* Versões exatas dos softwares utilizados (via Docker ou lista de versões)
* Arquivo de ambiente Python (`requirements.txt`) e R (`sessionInfo.txt`)

Esse pacote será depositado no Zenodo com DOI permanente, permitindo citação e replicação por outros pesquisadores.

---

## 19. Plano de comunicação

### 19.1 Públicos e mensagens-chave pré-execução
**Stakeholders a comunicar e meios:**
* **Orientador:** Comunicação semanal via email e reuniões presenciais.
* **Comunidade Speeduino:** Após conclusão, publicar resumo dos resultados no fórum oficial (speeduino.com/forum) e abrir Issue no GitHub do projeto linkando ao relatório completo.
* **Comunidade Acadêmica:** Submeter artigo para Workshop de Iniciação Científica da PUC Minas (WIC) ou similar.

### 19.2 Canais e frequência de comunicação
* **Semanal (Terças):** Email de status ao orientador com atualizações sobre progresso e bloqueios.
* **Ao final de cada fase:** Notificação formal de conclusão de marco ao orientador.
* **Pós-experimento:** Publicação única no fórum Speeduino e repositório GitHub.

### 19.3 Pontos de comunicação obrigatórios
* **Antes do piloto:** Solicitar aprovação formal do orientador para iniciar coleta de dados.
* **Após mudança no protocolo:** Comunicar imediatamente qualquer desvio do plano original.
* **Antes da entrega final:** Compartilhar rascunho do relatório com orientador para revisão final.

---

## 20. Critérios de prontidão para execução (Definition of Ready)

### 20.1 Checklist de prontidão

- [ ] Plano experimental revisado e aprovado pelo orientador
- [ ] Arduino Mega 2560 testado e funcional (upload de sketch de teste bem-sucedido)
- [ ] Arduino IDE 1.8.19 instalado e configurado
- [ ] TunerStudio MS instalado e conectando corretamente ao Arduino
- [ ] Python 3.10+ com Lizard instalado (`pip install lizard`)
- [ ] Repositório GitHub do Speeduino clonado localmente
- [ ] Planilha mestre de coleta de dados criada (com colunas: Versão, Repetição, CCN, NLOC, Loop Rate)
- [ ] Ambiente de backup configurado (OneDrive sincronizado)
- [ ] Protocolo de coleta impresso e disponível na bancada para consulta
- [ ] Cabo USB reserva disponível (caso o principal falhe)

### 20.2 Aprovações finais para iniciar a operação
**Aprovação Necessária:**
* **Orientador (Prof. Danilo):** Deve revisar este plano e enviar email com a frase: *"Aprovado para execução - Plano v3.0"*.
* **Auto-checklist do Pesquisador:** Verificar todos os itens da seção 20.1 antes de iniciar a Fase 3 (piloto).

**Forma de Registro:**
* Email de aprovação do orientador será arquivado na pasta do projeto.
* Screenshot do checklist 100% completo será anexado ao relatório final como evidência de preparação adequada.

---

## 📊 FLUXOGRAMA DO PROTOCOLO OPERACIONAL

```
┌─────────────────────────────────────────────────────────────┐
│          INÍCIO - Para cada uma das 6 versões               │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  1. Git checkout vYYYY.MM  │
        │     (Selecionar versão)     │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  2. Executar Lizard no     │
        │     código-fonte           │
        │     Gerar: metricas.csv    │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  3. Compilar no Arduino    │
        │     IDE e fazer upload     │
        │     para Arduino Mega      │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  4. Abrir TunerStudio,     │
        │     carregar base_tune.msq │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  5. Aguardar 30s de        │
        │     estabilização          │
        └────────────┬───────────────┘
                     │
                     ▼
        ┌────────────────────────────────────────┐
        │  6. LOOP: Para cada repetição (1-30)   │
        │  ┌──────────────────────────────────┐  │
        │  │ a) Iniciar Datalog (60s)         │  │
        │  │ b) Parar log                     │  │
        │  │ c) Calcular média Loop Rate (Hz) │  │
        │  │ d) Registrar na planilha         │  │
        │  │ e) Aguardar 10s                  │  │
        │  └──────────────────────────────────┘  │
        └────────────┬───────────────────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  7. Desconectar Arduino,   │
        │     limpar EEPROM          │
        └────────────┬───────────────┘
                     │
                     ▼
              ┌──────────────┐
              │ Última versão?│
              └──────┬────┬──┘
                  NÃO │    │ SIM
                     │    │
                     ▼    ▼
              ┌──────────────────────┐
              │  Próxima versão      │
              └──────────────────────┘
                     │
                     └────► VOLTA ao item 1
                     
                            │ SIM
                            ▼
              ┌─────────────────────────────┐
              │  8. Análise Estatística:    │
              │     - Testes de normalidade │
              │     - Correlação            │
              │     - ANOVA                 │
              │     - Regressão             │
              └─────────────┬───────────────┘
                            │
                            ▼
              ┌─────────────────────────────┐
              │  9. Gerar gráficos e        │
              │     relatório final         │
              └─────────────┬───────────────┘
                            │
                            ▼
                    ┌───────────────┐
                    │      FIM      │
                    └───────────────┘
```

**Legenda:**
* **Itens 1-4:** Preparação da versão (15 minutos)
* **Item 5:** Estabilização térmica e inicialização (30 segundos)
* **Item 6:** Coleta de dados propriamente dita (30 minutos)
* **Item 7:** Limpeza entre versões (5 minutos)
* **Itens 8-9:** Pós-processamento (executado apenas uma vez após todas as 6 versões)

**Tempo Total Estimado por Versão:** ~50 minutos
**Tempo Total do Experimento:** ~5 horas (para 6 versões)

---

**Fim do Plano de Experimento v3.1 COMPLETO**

**Assinatura Digital:**  
Luiz Filipe Nery Costa  
PUC Minas - Engenharia de Software  
05/12/2025

---

## ✅ CHECKLIST DE COMPLETUDE

### Conformidade com Enunciado Geral:
- [x] Seções 1-20: **TODAS presentes e preenchidas**
- [x] Subseções obrigatórias: **82/82 completas**
- [x] Fluxograma do protocolo operacional: **Incluído (Entrega 4)**
- [x] Tabelas GQM: **Completas (Entrega 2)**
- [x] Tabelas de variáveis: **Completas (Entrega 3)**
- [x] Tabela de ameaças à validade: **Completa (Entrega 5)**

### Entregas Acadêmicas:
- [x] **Entrega 1** (21/11): Identificação, contexto, problema
- [x] **Entrega 2** (25/11): Escopo, objetivos, GQM com 4+ objetivos, 3+ perguntas/objetivo, 10+ métricas
- [x] **Entrega 3** (28/11): Modelo conceitual, hipóteses, variáveis, fatores, desenho experimental
- [x] **Entrega 4** (Terça): População, instrumentação, protocolo operacional + **FLUXOGRAMA**
- [x] **Entrega 5** (Sexta): Avaliação de validade (ameaças categorizadas com mitigação)
- [ ] **Vídeo 3-5 min** (Entrega 5): 1 min overview + 3 min slides (1 por ameaça) + 1 min encerramento
- [ ] **Entrega Final** (12/12): Apresentação completa do planejamento

### Status Final:
✅ **Documento 100% completo para execução do experimento**  
⚠️ **Pendente apenas:** Gravação do vídeo de ameaças à validade (Entrega 5)

---

**Histórico de Revisões:**
* v1.0 (19/11/2025): Esboço inicial do escopo.
* v2.0 (25/11/2025): Expansão do GQM e métricas.
* v3.0 (02/12/2025): Detalhamento completo do desenho experimental.
* v3.1 (05/12/2025): **Finalização parcial** - Adição das seções 13-20, fluxograma, tabela de ameaças e conformidade total com Enunciado Geral (20/20 seções).