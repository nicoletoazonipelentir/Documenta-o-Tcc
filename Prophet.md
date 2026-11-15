# Documentação Técnica — Prophet 

### 1.1. Características gerais

| Característica | Prophet CPA | Prophet Ghost (ROAS) |
|:---|:---:|:---:|
| Indicador previsto | CPA | ROAS |
| Biblioteca | Prophet 1.4.0 | Prophet 1.4.0 |
| Tendência | Linear | Linear |
| Transformação logarítmica | Sim | Sim |
| Validação | Walk-forward | Walk-forward |
| Janela de treinamento | **365 dias** | **50 dias** |
| Início da previsão | 01/06/2026 | 01/06/2026 |
| Fim da previsão | 31/08/2026 | 31/08/2026 |
| Horizonte | 92 dias | 92 dias |
| Regressores históricos | Sim | Sim |
| Regressores de calendário | Sim | Sim |
| Regressores de ciclo | Opcionais | Opcionais |

# 2. Prophet CPA

## 2.1. Parâmetros estruturais

| Parâmetro | Valor | Finalidade |
|:---|:---:|:---|
| `growth` | **linear** | Define a tendência principal do modelo. |
| `n_changepoints` | Padrão | Define a quantidade de pontos candidatos de mudança de tendência. |
| `changepoint_range` | Padrão | Define a região do histórico em que as mudanças podem ser identificadas. |
| `changepoint_prior_scale` | Padrão | Controla a flexibilidade da tendência. |
| `seasonality_mode` | **additive** | Define a forma de incorporação dos efeitos sazonais. |
| `seasonality_prior_scale` | Padrão | Controla a flexibilidade dos efeitos sazonais. |
| `holidays_prior_scale` | Padrão | Controla a flexibilidade dos efeitos de feriados nativos. |

## 2.2. Sazonalidades e incerteza

| Parâmetro | Configuração no código | Finalidade |
|:---|:---:|:---|
| `weekly_seasonality` | Padrão | Sazonalidade semanal automática. |
| `yearly_seasonality` | Padrão | Sazonalidade anual automática. |
| `daily_seasonality` | Padrão | Sazonalidade diária automática. |
| `mcmc_samples` | Não configurado | Controla a utilização de amostragem MCMC. |
| `interval_width` | Não configurado | Define a largura nominal dos intervalos de incerteza. |
| `uncertainty_samples` | Não configurado | Controla a quantidade de amostras para estimar intervalos de incerteza. |

## 2.3. Regularização dos regressores

| Grupo | Parâmetro | Valor |
|:---|:---|:---:|
| Ciclos | `CYCLE_PRIOR_SCALE` | **0.10** |
| Feriados | `HOLIDAY_PRIOR_SCALE` | **0.10** |
| Dias da semana | `WEEKDAY_PRIOR_SCALE` | **0.10** |
| Histórico de CPA | `CPA_HISTORY_PRIOR_SCALE` | **0.10** |

## 2.4. Parâmetros operacionais

| Parâmetro | Valor | Descrição |
|:---|:---:|:---|
| `USE_LOG_TARGET` | **True** | Aplica transformação logarítmica ao CPA. |
| `FILTRAR_Y_NAO_POSITIVO` | **True** | Remove valores menores ou iguais a zero. |
| `APLICAR_TETO` | **False** | Não aplica limite artificial superior à previsão. |
| `MIN_TRAIN_ROWS` | **10** | Quantidade mínima de observações para treinamento. |
| `DEFAULT_TRAIN_WINDOW` | **365** | Janela histórica de treinamento, em dias. |
| `DEFAULT_START` | 01/06/2026 | Data inicial da previsão. |
| `DEFAULT_N_DAYS` | **92** | Quantidade de dias previstos. |

## 2.5. Regressores utilizados no CPA

| Grupo | Variáveis | Objetivo |
|:---|:---|:---|
| Ciclo | `dia_no_ciclo`, `is_fase_inicio`, `is_fase_meio`, `is_fase_fim` | Identificar a posição e a fase dentro do ciclo. |
| Dias da semana | Segunda, terça, sexta, sábado e domingo | Capturar diferenças de comportamento conforme o dia da semana. |
| Feriados | `is_feriado_nacional_util` | Identificar feriados nacionais em dias úteis. |
| Natal | `is_natal`, `is_natal_janela_favoravel` | Representar o Natal e sua janela comercial. |
| Datas comerciais | `is_dia_das_maes`, `is_dia_dos_pais`, `is_black_friday` | Capturar efeitos associados às datas comerciais. |
| Carnaval | `is_carnaval_bloco`, `is_carnaval_pos` | Representar o Carnaval e o período posterior. |
| Histórico | `cpa_lag_1`, `cpa_lag_7`, `cpa_lag_14` | Incorporar valores anteriores do CPA. |
| Medianas | `cpa_mediana_3`, `cpa_mediana_7`, `cpa_mediana_14` | Representar o comportamento central em janelas anteriores. |

### Engenharia de atributos históricos

| Variável | Período analisado | Finalidade |
|:---|:---:|:---|
| Lag 1 | 1 dia | Capturar o comportamento mais recente. |
| Lag 7 | 7 dias | Identificar possíveis repetições semanais. |
| Lag 14 | 14 dias | Capturar padrões de duas semanas. |
| Mediana 3 | 3 dias anteriores | Representar o comportamento recente, reduzindo o efeito de extremos. |
| Mediana 7 | 7 dias anteriores | Representar o comportamento da última semana. |
| Mediana 14 | 14 dias anteriores | Representar o comportamento de curto prazo. |

# 3. Prophet Ghost (ROAS)

## 3.1. Parâmetros estruturais

| Parâmetro | Valor | Finalidade |
|:---|:---:|:---|
| `growth` | **linear** | Define a tendência principal do modelo. |
| `n_changepoints` | **10** | Define a quantidade de pontos candidatos de mudança. |
| `changepoint_range` | **0.90** | Permite buscar mudanças em até 90% do histórico. |
| `changepoint_prior_scale` | **0.01** | Aplica regularização forte à tendência. |
| `seasonality_mode` | **additive** | Define efeitos sazonais aditivos. |
| `seasonality_prior_scale` | **1.0** | Controla a flexibilidade das sazonalidades. |
| `holidays_prior_scale` | **1.0** | Controla a flexibilidade dos feriados nativos. |

## 3.2. Sazonalidades e incerteza

| Parâmetro | Valor | Finalidade |
|:---|:---:|:---|
| `weekly_seasonality` | **False** | Desativa a sazonalidade semanal automática. |
| `yearly_seasonality` | **False** | Desativa a sazonalidade anual automática. |
| `daily_seasonality` | **False** | Desativa a sazonalidade diária. |
| `uncertainty_samples` | **0** | Desativa a geração de intervalos de incerteza por amostragem. |
| `mcmc_samples` | Não configurado | Controla a utilização de amostragem MCMC. |
| `interval_width` | Não configurado | Define a largura nominal dos intervalos de incerteza. |

## 3.3. Regularização dos regressores

| Grupo | Parâmetro | Valor |
|:---|:---|:---:|
| Ciclos | `CYCLE_PRIOR_SCALE` | **0.10** |
| Feriados | `HOLIDAY_PRIOR_SCALE` | **0.15** |
| Dias da semana | `WEEKDAY_PRIOR_SCALE` | **0.10** |
| Histórico de ROAS | `ROAS_HISTORY_PRIOR_SCALE` | **0.10** |

## 3.4. Parâmetros operacionais

| Parâmetro | Valor | Descrição |
|:---|:---:|:---|
| `USE_LOG_TARGET` | **True** | Aplica transformação logarítmica ao ROAS. |
| `FILTRAR_Y_NAO_POSITIVO` | **False** | Mantém desativada a filtragem de valores não positivos do alvo. |
| `USAR_HISTORICO_ROAS` | **True** | Ativa os regressores históricos. |
| `APLICAR_REGRA_PARADA_TOTAL` | **False** | Não força previsões iguais a zero em dias de parada total. |
| `MIN_TRAIN_ROWS` | **10** | Quantidade mínima de observações para treinamento. |
| `DEFAULT_TRAIN_WINDOW` | **50** | Janela histórica de treinamento, em dias. |
| `DEFAULT_START` | 01/06/2026 | Data inicial da previsão. |
| `DEFAULT_END` | 31/08/2026 | Data final da previsão. |

## 3.5. Regressores utilizados no ROAS

| Grupo | Variáveis | Objetivo |
|:---|:---|:---|
| Ciclo | `dia_no_ciclo`, `is_fase_inicio`, `is_fase_meio`, `is_fase_fim` | Identificar a posição e a fase dentro do ciclo. |
| Dias da semana | Segunda, terça, quarta, sexta, sábado e domingo | Capturar diferenças conforme o dia da semana. |
| Feriados | `is_feriado_dia_util` | Identificar feriados em dias úteis. |
| Datas especiais | `is_semana_santa`, `is_carnaval_pos` | Representar eventos e períodos específicos. |
| Datas comerciais | `is_dia_das_maes`, `is_dia_dos_pais`, `is_dia_dos_namorados` | Capturar efeitos de datas comerciais. |
| Natal | `is_natal_janela_favoravel` | Representar a janela favorável ao redor do Natal. |
| Histórico | `roas_lag_1`, `roas_lag_7`, `roas_lag_14` | Incorporar valores anteriores do ROAS. |
| Medianas | `roas_mediana_3`, `roas_mediana_7`, `roas_mediana_14` | Representar o comportamento central em janelas anteriores. |

### Engenharia de atributos históricos

| Variável | Período analisado | Finalidade |
|:---|:---:|:---|
| Lag 1 | 1 dia | Capturar o comportamento mais recente. |
| Lag 7 | 7 dias | Identificar possíveis repetições semanais. |
| Lag 14 | 14 dias | Capturar padrões de duas semanas. |
| Mediana 3 | 3 dias anteriores | Representar o comportamento recente. |
| Mediana 7 | 7 dias anteriores | Representar o comportamento semanal. |
| Mediana 14 | 14 dias anteriores | Representar o comportamento de curto prazo. |

## 3.6. Calendário de datas especiais

| Evento | Janela considerada | Interpretação |
|:---|:---:|:---|
| Feriados em dias úteis | Próprio dia | Identifica o efeito do feriado na data específica. |
| Semana Santa | 0 a +2 dias | Considera a influência posterior ao evento. |
| Pós-Carnaval | +1 a +5 dias | Representa alterações após o Carnaval. |
| Dia das Mães | -6 a +1 dias | Considera o período anterior e posterior à data. |
| Dia dos Pais | -1 a 0 dias | Representa a proximidade da data comercial. |
| Natal | -2 a -1 e +1 a +6 dias | Considera uma janela de comportamento favorável. |
| Dia dos Namorados | -3 a 0 dias | Representa a antecipação comercial. |

# 4. Comparação direta: CPA x ROAS

## 4.1. Parâmetros do modelo

| Parâmetro | CPA | ROAS |
|:---|:---:|:---:|
| Growth | Linear | Linear |
| Changepoints | Padrão | 10 |
| Changepoint range | Padrão | 0.90 |
| Changepoint prior scale | Padrão | 0.01 |
| Seasonality mode | Additive | Additive |
| Seasonality prior scale | Padrão | 1.0 |
| Holidays prior scale | Padrão | 1.0 |
| Transformação logarítmica | Sim | Sim |
| Sazonalidade semanal | Padrão | False |
| Sazonalidade anual | Padrão | False |
| Sazonalidade diária | Padrão | False |

## 4.2. Regularização e operação

| Parâmetro | CPA | ROAS |
|:---|:---:|:---:|
| Prior de ciclos | 0.10 | 0.10 |
| Prior de feriados | 0.10 | 0.15 |
| Prior de dias da semana | 0.10 | 0.10 |
| Prior histórico | 0.10 | 0.10 |
| Janela de treinamento | **365 dias** | **50 dias** |
| Mínimo de observações | 10 | 10 |
| Filtragem de valores não positivos | True | False |
| Intervalos de incerteza | Não configurados | Desativados |
| Teto artificial | Desativado | Não aplicável |
| Regra de parada total | Não implementada | Desativada |

# 5. Diferenças metodológicas

## 5.1. Janela de treinamento

| Modelo | Janela | Estratégia |
|:---|:---:|:---|
| Prophet CPA | **365 dias** | Histórico amplo |
| Prophet ROAS | **50 dias** | Histórico recente |
