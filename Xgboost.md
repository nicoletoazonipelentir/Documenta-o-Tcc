# Documentação Técnica — XGBoost

## 1. Características gerais

### 1.1. Configuração dos modelos

| Característica | XGBoost CPA | XGBoost ROAS |
|:---|:---:|:---:|
| Indicador previsto | CPA | ROAS |
| Biblioteca | XGBoost (`XGBRegressor`) | XGBoost (`XGBRegressor`) |
| Tipo de modelo | Regressão supervisionada | Regressão supervisionada |
| Quantidade de atributos históricos | 21 lags | 21 lags |
| Atributos de calendário e ciclo | Sim | Não |
| Transformação logarítmica | Não | Sim |
| Horizonte de previsão | 1 dia | 1 dia |
| Período de treinamento | 01/01/2025 a 31/12/2025 | 01/01/2025 a 31/12/2025 |
| Início da avaliação | 01/06/2026 | 01/06/2026 |
| Fim da avaliação | 31/08/2026 | 31/08/2026 |
| Datas no período de avaliação | 92 | 92 |
| Atualização dos atributos históricos | Diária | Diária |
| Reajuste do modelo durante a avaliação | Não | Não |

### 1.2. Bibliotecas e ambiente

| Biblioteca/recurso | Utilização |
|:---|:---|
| Python | Linguagem de implementação |
| XGBoost | Construção dos modelos de regressão |
| NumPy | Transformações numéricas e cálculo de previsões |
| Pandas | Manipulação, organização e preparação dos dados |
| OpenPyXL | Leitura e geração de arquivos Excel |
| Google Colab | Ambiente de execução utilizado na implementação |


## 2. XGBoost CPA

### 2.1. Parâmetros estruturais

| Parâmetro | Valor | Finalidade |
|:---|:---:|:---|
| `n_estimators` | **250** | Quantidade de árvores construídas pelo modelo. |
| `max_depth` | **3** | Profundidade máxima permitida para cada árvore. |
| `learning_rate` | **0.05** | Taxa de contribuição de cada árvore no processo de boosting. |
| `min_child_weight` | **1** | Peso mínimo necessário para formar um novo nó. |
| `subsample` | **0.90** | Proporção de observações amostradas para cada árvore. |
| `colsample_bytree` | **0.80** | Proporção de atributos amostrados para cada árvore. |
| `gamma` | **0.10** | Ganho mínimo necessário para realizar uma divisão. |
| `reg_alpha` | **0** | Parâmetro de regularização L1. |
| `reg_lambda` | **2** | Parâmetro de regularização L2. |
| `objective` | `reg:squarederror` | Define a minimização do erro quadrático. |
| `random_state` | **42** | Define a semente aleatória para reprodutibilidade. |
| `n_jobs` | **-1** | Permite utilizar todos os processadores disponíveis. |

### 2.2. Parâmetros operacionais

| Parâmetro | Valor/configuração | Descrição |
|:---|:---:|:---|
| `N_LAGS` | **21** | Quantidade de defasagens utilizadas como atributos. |
| Variável-alvo | CPA | Indicador previsto pelo modelo. |
| `USE_LOG_TARGET` | **False** | Não aplica transformação logarítmica ao CPA. |
| Transformação inversa | Não aplicável | O alvo permanece na escala original. |
| Limite inferior da previsão | **0** | Previsões negativas são limitadas a zero. |
| Período de treinamento | **365 dias** | De 01/01/2025 a 31/12/2025. |
| Período de avaliação | **92 dias** | De 01/06/2026 a 31/08/2026. |
| Horizonte | **1 dia** | Previsão do dia seguinte. |
| Atualização dos lags | Diária | Incorpora os valores reais observados até o dia anterior. |
| Reajuste dos parâmetros | Não realizado | O modelo permanece ajustado com os dados de 2025. |

### 2.3. Atributos utilizados no CPA

| Grupo | Variável | Finalidade |
|:---|:---|:---|
| Histórico | `cpa_lag_1` a `cpa_lag_21` | Incorporar os valores anteriores do CPA. |
| Ciclo | `dia_no_ciclo` | Identificar a posição do dia dentro do ciclo comercial. |
| Fase do ciclo | `fase_ciclo_num` | Identificar a fase do ciclo: início, meio ou fim. |
| Calendário | `dia_semana_num` | Representar numericamente o dia da semana. |
| Calendário | `is_weekend` | Identificar finais de semana. |
| Calendário | `is_holiday` | Identificar feriados. |

### 2.4. Codificação das fases do ciclo

| Fase | Codificação |
|:---|:---:|
| Início | **1** |
| Meio | **2** |
| Fim | **3** |

A variável `fase_ciclo_num` é obtida por meio do mapeamento das categorias textuais de fase para valores numéricos.

### 2.5. Engenharia de atributos históricos

| Variável | Período considerado | Finalidade |
|:---|:---:|:---|
| `cpa_lag_1` | 1 dia anterior | Capturar o valor mais recente do CPA. |
| `cpa_lag_2` | 2 dias anteriores | Incorporar o histórico de curto prazo. |
| `cpa_lag_3` | 3 dias anteriores | Representar a evolução recente do indicador. |
| `...` | `...` | `...` |
| `cpa_lag_21` | 21 dias anteriores | Incorporar o histórico das três semanas anteriores. |

### 2.6. Transformação da variável-alvo

| Etapa | Configuração |
|:---|:---|
| Transformação logarítmica | Não aplicada |
| Escala de treinamento | CPA original |
| Transformação inversa | Não aplicável |
| Tratamento de previsões negativas | Limitadas a zero |

A previsão final é definida por:

\[
\hat{y}_{CPA}=\max(\hat{y},0)
\]

## 3. XGBoost ROAS

### 3.1. Parâmetros estruturais

| Parâmetro | Valor | Finalidade |
|:---|:---:|:---|
| `n_estimators` | **300** | Quantidade de árvores construídas pelo modelo. |
| `max_depth` | **6** | Profundidade máxima permitida para cada árvore. |
| `learning_rate` | **0.05** | Taxa de contribuição de cada árvore no processo de boosting. |
| `min_child_weight` | **1** | Peso mínimo necessário para formar um novo nó. |
| `subsample` | **0.80** | Proporção de observações amostradas para cada árvore. |
| `colsample_bytree` | **0.90** | Proporção de atributos amostrados para cada árvore. |
| `gamma` | **0** | Ganho mínimo necessário para realizar uma divisão. |
| `reg_alpha` | **0** | Parâmetro de regularização L1. |
| `reg_lambda` | **5** | Parâmetro de regularização L2. |
| `objective` | `reg:squarederror` | Define a minimização do erro quadrático. |
| `random_state` | **42** | Define a semente aleatória para reprodutibilidade. |
| `n_jobs` | **-1** | Permite utilizar todos os processadores disponíveis. |

### 3.2. Parâmetros operacionais

| Parâmetro | Valor/configuração | Descrição |
|:---|:---:|:---|
| `N_LAGS` | **21** | Quantidade de defasagens utilizadas como atributos. |
| Variável-alvo | ROAS | Indicador previsto pelo modelo. |
| `USE_LOG_TARGET` | **True** | Aplica transformação logarítmica ao ROAS. |
| Transformação utilizada | `log1p` | Aplica \(\ln(1+y)\) ao alvo. |
| Transformação inversa | `expm1` | Retorna a previsão à escala original. |
| Limite inferior da previsão | **0** | Previsões negativas são limitadas a zero. |
| Período de treinamento | **365 dias** | De 01/01/2025 a 31/12/2025. |
| Período de avaliação | **92 dias** | De 01/06/2026 a 31/08/2026. |
| Horizonte | **1 dia** | Previsão do dia seguinte. |
| Atualização dos lags | Diária | Incorpora os valores reais observados até o dia anterior. |
| Reajuste dos parâmetros | Não realizado | O modelo permanece ajustado com os dados de 2025. |

### 3.3. Atributos utilizados no ROAS

| Grupo | Variáveis | Finalidade |
|:---|:---|:---|
| Histórico | `roas_lag_1` a `roas_lag_21` | Incorporar os valores anteriores do ROAS. |

### 3.4. Engenharia de atributos históricos

| Variável | Período considerado | Finalidade |
|:---|:---:|:---|
| `roas_lag_1` | 1 dia anterior | Capturar o valor mais recente do ROAS. |
| `roas_lag_2` | 2 dias anteriores | Incorporar o histórico de curto prazo. |
| `roas_lag_3` | 3 dias anteriores | Representar a evolução recente do indicador. |
| `...` | `...` | `...` |
| `roas_lag_21` | 21 dias anteriores | Incorporar o histórico das três semanas anteriores. |

### 3.5. Transformação da variável-alvo

| Etapa | Configuração |
|:---|:---|
| Transformação logarítmica | Aplicada |
| Função de transformação | `np.log1p(y)` |
| Escala de treinamento | \(\ln(1+ROAS)\) |
| Transformação inversa | `np.expm1(predição)` |
| Tratamento de previsões negativas | Limitadas a zero |

A transformação é definida por:

\[
y'=\ln(1+y)
\]

Após a previsão, o resultado retorna à escala original:

\[
\hat{y}=\exp(\hat{y}')-1
\]

A previsão final é definida por:

\[
\hat{y}_{ROAS}=\max(\exp(\hat{y}')-1,0)
\]

## 4. Comparação direta: CPA × ROAS

### 4.1. Parâmetros dos modelos

| Parâmetro | CPA | ROAS |
|:---|:---:|:---:|
| Número de estimadores | 250 | 300 |
| Profundidade máxima | 3 | 6 |
| Taxa de aprendizagem | 0.05 | 0.05 |
| Min child weight | 1 | 1 |
| Subsample | 0.90 | 0.80 |
| Colsample bytree | 0.80 | 0.90 |
| Gamma | 0.10 | 0 |
| Regularização L1 | 0 | 0 |
| Regularização L2 | 2 | 5 |
| Função objetivo | `reg:squarederror` | `reg:squarederror` |
| Semente aleatória | 42 | 42 |
| Quantidade de lags | 21 | 21 |

### 4.2. Atributos e transformações

| Componente | CPA | ROAS |
|:---|:---:|:---:|
| Lags da própria métrica | Sim | Sim |
| Dia do ciclo | Sim | Não |
| Fase do ciclo | Sim | Não |
| Dia da semana | Sim | Não |
| Fim de semana | Sim | Não |
| Feriados | Sim | Não |
| Estatísticas móveis | Não | Não |
| Variáveis operacionais | Não | Não |
| Transformação logarítmica | Não | Sim |
| Transformação inversa | Não aplicável | Sim |
| Previsões negativas limitadas a zero | Sim | Sim |

### 4.3. Estratégia de treinamento

| Etapa | CPA | ROAS |
|:---|:---:|:---:|
| Período de treinamento | 2025 | 2025 |
| Quantidade de observações no período | 365 dias | 365 dias |
| Período de avaliação | Jun.–ago./2026 | Jun.–ago./2026 |
| Quantidade de datas avaliadas | 92 | 92 |
| Horizonte | 1 dia | 1 dia |
| Parâmetros fixos durante a avaliação | Sim | Sim |
| Atualização diária dos lags com valores reais | Sim | Sim |
| Reajuste diário do modelo | Não | Não |

## 5. Procedimento de treinamento e avaliação

### 5.1. Separação temporal

| Etapa | Data inicial | Data final | Quantidade de dias |
|:---|:---:|:---:|---:|
| Treinamento | 01/01/2025 | 31/12/2025 | **365** |
| Avaliação | 01/06/2026 | 31/08/2026 | **92** |

### 5.2. Atualização dos atributos históricos

| Elemento | Procedimento |
|:---|:---|
| Horizonte | Um dia à frente |
| Histórico utilizado | 21 valores observados anteriores |
| Origem dos lags | Série original observada |
| Atualização | Diária |
| Uso de previsões anteriores nos lags | Não |
| Reajuste dos modelos durante a avaliação | Não |
| Atualização dos hiperparâmetros | Não |


### 5.3. Tratamento de valores ausentes

| Etapa | Procedimento identificado |
|:---|:---|
| Variável-alvo no treinamento | São utilizadas observações com alvo não ausente. |
| Atributos históricos | Valores ausentes podem ser tratados pelo mecanismo nativo do XGBoost. |
| Datas | Datas inválidas e duplicadas são rejeitadas na validação da base. |
| Calendário | A base diária é validada entre 01/01/2025 e 31/08/2026. |
| Avaliação | Linhas sem realizado ou previsão disponível são desconsideradas nas métricas. |
| Valores infinitos | Convertidos em ausentes antes do cálculo das métricas. |

## 6. Métricas de avaliação

### 6.1. Erro Absoluto Médio (MAE)

\[
MAE=\frac{1}{n}\sum_{i=1}^{n}|y_i-\hat{y}_i|
\]

| Elemento | Descrição |
|:---|:---|
| \(n\) | Quantidade de observações válidas |
| \(y_i\) | Valor observado |
| \(\hat{y}_i\) | Valor previsto |
| Interpretação | Média dos erros absolutos |

### 6.2. Raiz do Erro Quadrático Médio (RMSE)

\[
RMSE=\sqrt{\frac{1}{n}\sum_{i=1}^{n}(y_i-\hat{y}_i)^2}
\]

| Elemento | Descrição |
|:---|:---|
| \(n\) | Quantidade de observações válidas |
| \(y_i\) | Valor observado |
| \(\hat{y}_i\) | Valor previsto |
| Interpretação | Raiz da média dos erros quadráticos |

### 6.3. Períodos de cálculo

| Período | Data inicial | Data final |
|:---|:---:|:---:|
| Junho/2026 | 01/06/2026 | 30/06/2026 |
| Julho/2026 | 01/07/2026 | 31/07/2026 |
| Agosto/2026 | 01/08/2026 | 31/08/2026 |
| Trimestre completo | 01/06/2026 | 31/08/2026 |

## 7. Limitações e considerações técnicas

| Aspecto | Consideração |
|:---|:---|
| Extrapolação de tendência | Árvores de decisão não extrapolam tendências temporais diretamente. |
| Dependência histórica | A previsão depende dos atributos construídos a partir dos valores anteriores. |
| Mudanças abruptas | Alterações fora dos padrões aprendidos podem reduzir a precisão. |
| Parâmetros fixos | Os modelos não são reajustados durante o período de avaliação. |
| Atualização histórica | Os lags são atualizados com valores reais observados. |
| Diferenças entre indicadores | CPA utiliza atributos de ciclo e calendário; ROAS utiliza somente seus lags. |
| Transformação do ROAS | A previsão é revertida para a escala original antes da avaliação. |
| Escala do CPA | O CPA é previsto diretamente em sua escala original. |


