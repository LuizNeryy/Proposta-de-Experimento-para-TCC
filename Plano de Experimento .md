# Plano de Experimento – Scoping e Planejamento

## 1. Identificação básica

### 1.1 Título do experimento
**Análise Longitudinal da Correlação entre Complexidade de Código e Desempenho Temporal (Loop Rate) no Firmware Speeduino em Hardware Limitado.**

### 1.2 ID / código
`EXP-MES-2025-SPEEDUINO-FINAL`

### 1.3 Versão do documento e histórico de revisão
* **Versão Atual:** v3.0 (Versão Final Estendida)
* **Histórico:**
    * v1.0 (19/11/2025): Esboço inicial do escopo.
    * v2.0 (25/11/2025): Expansão do GQM e métricas.
    * v3.0 (02/12/2025): Detalhamento completo do desenho experimental, hipóteses estatísticas e protocolo de execução para trabalho final.

### 1.4 Datas (criação, última atualização)
* **Criação:** 19/11/2025
* **Última atualização:** 02/12/2025

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