# Documentação Técnica — ARIMA

## 1. Características gerais

### 1.1. Configuração dos modelos

| Característica                           |         ARIMA CPA         |        ARIMA ROAS         |
|:-----------------------------------------|:-------------------------:|:-------------------------:|
| Indicador previsto                       |            CPA            |           ROAS            |
| Biblioteca                               |        Statsmodels        |        Statsmodels        |
| Tipo de modelo                           | Série temporal univariada | Série temporal univariada |
| Modelo                                   |       ARIMA(0,1,1)        |       ARIMA(0,1,2)        |
| Ordem autorregressiva (`p`)              |             0             |             0             |
| Ordem de diferenciação (`d`)             |             1             |             1             |
| Ordem de médias móveis (`q`)             |             1             |             2             |
| Janela de treinamento                    |          21 dias          |          21 dias          |
| Horizonte de previsão                    |           1 dia           |           1 dia           |
| Transformação logarítmica                |            Não            |            Não            |
| Sazonalidade explícita                   |            Não            |            Não            |
| Variáveis exógenas                       |            Não            |            Não            |
| Atualização da janela                    |          Diária           |          Diária           |
| Reestimação dos parâmetros               |          Diária           |          Diária           |
| Mínimo de observações válidas por janela |            10             |            10             |
| Período de avaliação                     |  01/06/2026 a 31/08/2026  |  01/06/2026 a 31/08/2026  |
| Datas no período de avaliação            |            92             |            92             |

### 1.2. Bibliotecas e ambiente

| Biblioteca/recurso | Utilização                                            |
|:-------------------|:------------------------------------------------------|
| Python             | Linguagem de implementação                            |
| Statsmodels        | Especificação, estimação e previsão dos modelos ARIMA |
| ACF                | Análise da autocorrelação das observações da série    |
| PACF               | Análise da autocorrelação parcial                     |
| Ljung-Box          | Diagnóstico de autocorrelação dos resíduos            |
| MAE                | Cálculo do erro absoluto médio                        |
| RMSE               | Cálculo da raiz do erro quadrático médio              |

## 2. ARIMA CPA

### 2.1. Parâmetros estruturais

| Parâmetro       |      Valor       | Finalidade                                                 |
|:----------------|:----------------:|:-----------------------------------------------------------|
| `p`             |      **0**       | Ordem autorregressiva; não há termos AR no modelo.         |
| `d`             |      **1**       | Ordem de diferenciação aplicada à série.                   |
| `q`             |      **1**       | Ordem de médias móveis; considera uma defasagem dos erros. |
| Ordem do modelo | **ARIMA(0,1,1)** | Especificação selecionada para previsão do CPA.            |

### 2.2. Parâmetros operacionais

| Parâmetro                     | Valor/configuração | Descrição                                                   |
|:------------------------------|:------------------:|:------------------------------------------------------------|
| Variável-alvo                 |        CPA         | Indicador previsto pelo modelo.                             |
| `WINDOW_SIZE`                 |    **21 dias**     | Quantidade de dias históricos utilizada em cada ajuste.     |
| Horizonte de previsão         |     **1 dia**      | Previsão do dia imediatamente seguinte à janela.            |
| `p`                           |       **0**        | Não utiliza componente autorregressivo.                     |
| `d`                           |       **1**        | Aplica uma diferenciação.                                   |
| `q`                           |       **1**        | Utiliza um termo de médias móveis.                          |
| Transformação logarítmica     |    Não aplicada    | O alvo permanece na escala original do CPA.                 |
| Transformação inversa         |   Não aplicável    | Não há transformação do alvo a reverter.                    |
| Sazonalidade                  |   Não utilizada    | Não foi especificado componente sazonal.                    |
| Variáveis exógenas            |   Não utilizadas   | O modelo utiliza somente o histórico da própria métrica.    |
| Mínimo de observações válidas |       **10**       | Quantidade mínima de valores válidos exigida na janela.     |
| Atualização da janela         |       Diária       | A janela avança após a disponibilização do valor observado. |
| Reestimação dos parâmetros    |       Diária       | Os parâmetros são reestimados em cada nova janela.          |
| Período de avaliação          |    **92 dias**     | De 01/06/2026 a 31/08/2026.                                 |

### 2.3. Especificação da série

| Componente                       | Configuração                  |
|:---------------------------------|:------------------------------|
| Série utilizada                  | CPA diário                    |
| Histórico considerado            | 21 dias anteriores à previsão |
| Componente autorregressivo (AR)  | Não utilizado (`p = 0`)       |
| Diferenciação (I)                | Uma diferenciação (`d = 1`)   |
| Componente de médias móveis (MA) | Uma defasagem (`q = 1`)       |
| Sazonalidade                     | Não utilizada                 |
| Regressores externos             | Não utilizados                |
| Transformação do alvo            | Não aplicada                  |
| Escala da previsão               | Escala original do CPA        |

### 2.4. Procedimento de previsão

| Etapa                     | Procedimento                                                                   |
|:--------------------------|:-------------------------------------------------------------------------------|
| Formação da janela        | Seleção dos 21 dias anteriores à data prevista.                                |
| Validação da janela       | Verificação da existência de pelo menos 10 observações válidas.                |
| Ajuste                    | Estimação do ARIMA(0,1,1) com os dados elegíveis da janela.                    |
| Previsão                  | Geração de previsão para um dia à frente.                                      |
| Incorporação do realizado | O valor observado é incorporado após sua disponibilização.                     |
| Atualização               | A janela é deslocada para considerar os 21 dias anteriores à próxima previsão. |
| Reestimação               | Os parâmetros do modelo são estimados novamente em cada iteração.              |

### 2.5. Tratamento de valores ausentes

| Elemento                      | Configuração                                                                                   |
|:------------------------------|:-----------------------------------------------------------------------------------------------|
| Valores ausentes na série     | Mantidos como `NaN` na base.                                                                   |
| Preenchimento por zero        | Não realizado.                                                                                 |
| Interpolação                  | Não realizada.                                                                                 |
| Mínimo de observações válidas | 10 por janela.                                                                                 |
| Critério de elegibilidade     | A janela precisa conter pelo menos 10 observações válidas para que a previsão seja processada. |

## 3. ARIMA ROAS

### 3.1. Parâmetros estruturais

| Parâmetro       |      Valor       | Finalidade                                                   |
|:----------------|:----------------:|:-------------------------------------------------------------|
| `p`             |      **0**       | Ordem autorregressiva; não há termos AR no modelo.           |
| `d`             |      **1**       | Ordem de diferenciação aplicada à série.                     |
| `q`             |      **2**       | Ordem de médias móveis; considera duas defasagens dos erros. |
| Ordem do modelo | **ARIMA(0,1,2)** | Especificação selecionada para previsão do ROAS.             |

### 3.2. Parâmetros operacionais

| Parâmetro                     | Valor/configuração | Descrição                                                   |
|:------------------------------|:------------------:|:------------------------------------------------------------|
| Variável-alvo                 |        ROAS        | Indicador previsto pelo modelo.                             |
| `WINDOW_SIZE`                 |    **21 dias**     | Quantidade de dias históricos utilizada em cada ajuste.     |
| Horizonte de previsão         |     **1 dia**      | Previsão do dia imediatamente seguinte à janela.            |
| `p`                           |       **0**        | Não utiliza componente autorregressivo.                     |
| `d`                           |       **1**        | Aplica uma diferenciação.                                   |
| `q`                           |       **2**        | Utiliza dois termos de médias móveis.                       |
| Transformação logarítmica     |    Não aplicada    | O alvo permanece na escala original do ROAS.                |
| Transformação inversa         |   Não aplicável    | Não há transformação do alvo a reverter.                    |
| Sazonalidade                  |   Não utilizada    | Não foi especificado componente sazonal.                    |
| Variáveis exógenas            |   Não utilizadas   | O modelo utiliza somente o histórico da própria métrica.    |
| Mínimo de observações válidas |       **10**       | Quantidade mínima de valores válidos exigida na janela.     |
| Atualização da janela         |       Diária       | A janela avança após a disponibilização do valor observado. |
| Reestimação dos parâmetros    |       Diária       | Os parâmetros são reestimados em cada nova janela.          |
| Período de avaliação          |    **92 dias**     | De 01/06/2026 a 31/08/2026.                                 |

### 3.3. Especificação da série

| Componente                       | Configuração                  |
|:---------------------------------|:------------------------------|
| Série utilizada                  | ROAS diário                   |
| Histórico considerado            | 21 dias anteriores à previsão |
| Componente autorregressivo (AR)  | Não utilizado (`p = 0`)       |
| Diferenciação (I)                | Uma diferenciação (`d = 1`)   |
| Componente de médias móveis (MA) | Duas defasagens (`q = 2`)     |
| Sazonalidade                     | Não utilizada                 |
| Regressores externos             | Não utilizados                |
| Transformação do alvo            | Não aplicada                  |
| Escala da previsão               | Escala original do ROAS       |

### 3.4. Procedimento de previsão

| Etapa                     | Procedimento                                                                   |
|:--------------------------|:-------------------------------------------------------------------------------|
| Formação da janela        | Seleção dos 21 dias anteriores à data prevista.                                |
| Validação da janela       | Verificação da existência de pelo menos 10 observações válidas.                |
| Ajuste                    | Estimação do ARIMA(0,1,2) com os dados elegíveis da janela.                    |
| Previsão                  | Geração de previsão para um dia à frente.                                      |
| Incorporação do realizado | O valor observado é incorporado após sua disponibilização.                     |
| Atualização               | A janela é deslocada para considerar os 21 dias anteriores à próxima previsão. |
| Reestimação               | Os parâmetros do modelo são estimados novamente em cada iteração.              |

### 3.5. Tratamento de valores ausentes

| Elemento                      | Configuração                                                                                   |
|:------------------------------|:-----------------------------------------------------------------------------------------------|
| Valores ausentes na série     | Mantidos como `NaN` na base.                                                                   |
| Preenchimento por zero        | Não realizado.                                                                                 |
| Interpolação                  | Não realizada.                                                                                 |
| Mínimo de observações válidas | 10 por janela.                                                                                 |
| Critério de elegibilidade     | A janela precisa conter pelo menos 10 observações válidas para que a previsão seja processada. |

## 4. Comparação direta: CPA × ROAS

### 4.1. Parâmetros dos modelos

| Parâmetro                     |   CPA   |  ROAS   |
|:------------------------------|:-------:|:-------:|
| Ordem ARIMA                   | (0,1,1) | (0,1,2) |
| Ordem autorregressiva (`p`)   |    0    |    0    |
| Ordem de diferenciação (`d`)  |    1    |    1    |
| Ordem de médias móveis (`q`)  |    1    |    2    |
| Janela de treinamento         | 21 dias | 21 dias |
| Horizonte                     |  1 dia  |  1 dia  |
| Transformação logarítmica     |   Não   |   Não   |
| Sazonalidade                  |   Não   |   Não   |
| Variáveis exógenas            |   Não   |   Não   |
| Atualização da janela         | Diária  | Diária  |
| Reestimação dos parâmetros    | Diária  | Diária  |
| Mínimo de observações válidas |   10    |   10    |

### 4.2. Configurações da série

| Componente                     |   CPA   |  ROAS   |
|:-------------------------------|:-------:|:-------:|
| Histórico da própria métrica   |   Sim   |   Sim   |
| Diferenciação                  | Ordem 1 | Ordem 1 |
| Componente autorregressivo     |   Não   |   Não   |
| Componente de médias móveis    | Ordem 1 | Ordem 2 |
| Sazonalidade explícita         |   Não   |   Não   |
| Dia da semana como regressor   |   Não   |   Não   |
| Ciclo comercial como regressor |   Não   |   Não   |
| Feriados como regressores      |   Não   |   Não   |
| Variáveis de investimento      |   Não   |   Não   |
| Transformação logarítmica      |   Não   |   Não   |

## 5. Procedimento de treinamento e avaliação

### 5.1. Período de avaliação

| Etapa     | Data inicial | Data final | Quantidade de dias |
|:----------|:------------:|:----------:|-------------------:|
| Avaliação |  01/06/2026  | 31/08/2026 |             **92** |

### 5.2. Estratégia de previsão móvel (*rolling window*)

| Elemento                              | Procedimento                                       |
|:--------------------------------------|:---------------------------------------------------|
| Tamanho da janela                     | 21 dias                                            |
| Horizonte                             | Um dia à frente                                    |
| Histórico utilizado                   | Observações dos 21 dias anteriores à data prevista |
| Atualização da janela                 | Diária                                             |
| Incorporação do valor real            | Após a disponibilização do valor observado         |
| Reestimação dos parâmetros            | Realizada a cada nova janela                       |
| Atualização das ordens `p`, `d` e `q` | Não realizada durante a avaliação                  |
| Utilização de valores futuros         | Não realizada                                      |

### 5.3. Tratamento de valores ausentes

| Elemento                      | Procedimento                                                  |
|:------------------------------|:--------------------------------------------------------------|
| Valores ausentes              | Mantidos como `NaN`.                                          |
| Preenchimento por zero        | Não realizado.                                                |
| Interpolação                  | Não realizada.                                                |
| Mínimo de observações válidas | 10 por janela.                                                |
| Processamento da previsão     | Condicionado à disponibilidade mínima de observações válidas. |

**Nota:** a documentação do procedimento de manutenção dos `NaN` não
substitui a confirmação da configuração efetiva de tratamento de valores
ausentes utilizada na estimação do Statsmodels.

## 6. Métricas de avaliação

### 6.1. Erro Absoluto Médio (MAE)

\[
MAE = \frac{1}{n}\sum_{i=1}^{n} |y_i - \hat{y}_i|
\]

| Elemento      | Descrição                                                          |
|:--------------|:-------------------------------------------------------------------|
| \(n\)         | Quantidade de observações válidas.                                 |
| (y_i)         | Valor observado.                                                   |
| (\_i)         | Valor previsto.                                                    |
| Interpretação | Média dos erros absolutos entre os valores previstos e observados. |

### 6.2. Raiz do Erro Quadrático Médio (RMSE)

\[
RMSE = \sqrt{\frac{1}{n}\sum_{i=1}^{n}(y_i-\hat{y}_i)^2}
\]

| Elemento      | Descrição                                                                              |
|:--------------|:---------------------------------------------------------------------------------------|
| \(n\)         | Quantidade de observações válidas.                                                     |
| (y_i)         | Valor observado.                                                                       |
| (\_i)         | Valor previsto.                                                                        |
| Interpretação | Raiz da média dos erros quadráticos, atribuindo maior peso a erros de maior magnitude. |

### 6.3. Períodos de cálculo

| Período            | Data inicial | Data final |
|:-------------------|:------------:|:----------:|
| Junho/2026         |  01/06/2026  | 30/06/2026 |
| Julho/2026         |  01/07/2026  | 31/07/2026 |
| Agosto/2026        |  01/08/2026  | 31/08/2026 |
| Trimestre completo |  01/06/2026  | 31/08/2026 |
