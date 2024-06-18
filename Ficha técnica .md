# Ficha técnica

## Análise de dados

### Projeto 3 - Risco relativo

- **Objetivo:**
    
    O banco propõe automatizar o processo de análise de crédito utilizando técnicas avançadas de análise de dados para aumentar a eficiência, precisão e rapidez na avaliação de pedidos. Além disso, pretende integrar uma métrica existente para identificar clientes com pagamento atrasado na nova classificação de risco automatizada. O objetivo é identificar **perfis de risco de inadimplência, criar uma pontuação de crédito e classificar clientes** em diferentes categorias de risco. Isso permitirá decisões mais informadas sobre concessão de crédito, reduzindo o risco de empréstimos não reembolsáveis e fortalecendo a solidez financeira e a eficiência operacional do banco.
    
- **Equipe:**
    
    Bruna Paiva.
    
- **Ferramentas e Tecnologias:**
    - Big Query, Looker Studio, Google Colab e GitHub.
    - Linguagem SQL no BigQuery e Python no Google Colab.
- **Processamento e análises:**
    - A primeira query foi na tabela default e não foi encontrado nenhum dado nulo.
    - Na tabela loans_detail também não foi encontrado nulos.
    - Também não foi encontrado nulos na tabela loans_outstanding.
    - Foi encontrado 7199 nulos na coluna last_month_salary e 943 da coluna number_dependent  da tabela user_info.
    
    Os dados nulos encontrados foram nas variaveis last_month_salary e number_dependents.
    
    Optei por utilizar os valores da média que é de 6675 para substituir os valores para não perder 
    
    tantos dados importantes, já que não afetaria significatemente a média.
    
    ---
    
    - Na tabela default  não foi encontrado nenhum dado duplicado.
    - Na tabela loans_detail não foi encontrado nenhum dado duplicado.
    - Na tabela loans_outstanding não foi encontrado dados duplicados.
    - Na tabela user_info. não foi encontrado nenhum dado duplicado.
    
    ---
    
    Criando uma tabela auxilar clientes_questionaveis para identificar os clientes com last_month_salary nulos e que apresentavam default_flag em 1 encontrei 130 clientes_duvidosos de 683 clientes que não pagaram.
    
    ---
    
    Utilizei a correlação e desvio padrão para entender qual das informações de atrasos no pagamento seria melhor para permanecer na base de dados.  A correlação de 90 e 60 dias foram as melhores e no desvio padrão o desvio maior foi nos 90 dias,. Foi escolhido a variável more_90_days_overdue.
    
    Despois de avaliar melhor as tabelas, verifiquei que os dados da tabela loan_outstanding apresentam variáveis fora de escopo, como no caso do loan_id que tem 305.335 dados. Resolvi utilizar dessa tabela a variável loan_type e criar três variaveis: quantidade_emprestimos - total de emprestimos que cada cliente fez; emprestimos_imobiliario - quantos emprestismo do tipo imobiliarios cada cliente fez; e emprestimo_outros - quantos emprestimos do tipo outros foram realizados por cada cliente.
    
    ---
    
    Escolhi padronizar as strings das tabelas loans_outstanding e user_info, na primeira tabela modifiquei e padronizei para apenas dois termos da coluna loan_type em “Imobiliários” e “Outros” que ficou a coluna como tipos_emprestimos, na segunda tabela modifiquei a coluna sex para sexo, colocando “Feminino” e “Masculino”.
    
    ---
    
    Na tentativa de de imputar os outliers da base de dados nas variáveis ultimo_salario, eu inicialmente iria usar winsorização para reduzir a discrepância dos dados, mas como só consegui o valor da média 6.221 e de desvio padrão de 3.087 eu não achei bom o suficiente. 
    
    Então optei por delimitar  no ultimo_salario quais dados seriam alterados para diminuir o desvio padrão com as métricas da winsorização de percentis de 5% e 95% para saber quais dados precisavam ser modificados e imputei os valores da mediana nesses outliers, alterando 3.477 dados de salário e obtendo a média de 6.120 com desvio padrão de 2.421. 
    
    Avaliando quantos dados discrepantes que haviam no linhas_de_credito_nao_garantidas_por_ativos_pessoais notei que existiam 145 dados variandos em 56 valores diferentes, e apliquei a winsorização para imputar os dados e padronizar. A média e o desvio padrão ficaram em 0, já que 26.195 desses dados estão no valor 0 e 9.660 estão no valor 1.
    
    Na variável indice_endividamento encontrei 22.400 valores em 0 e 5.833 em 1, cerca de 7.767 dados variam 3.396 vezes entre valores de 2 e 307.001, substitui os valores de limite_inferior no percentis de 5%  e os valores de limite_superior no percentis de 95% por valores da mediana.
    
    ---
    
    Depois de diminuir os outliers do último salário, optei por modificar alguns dados da tabela para gerar a tabela definitiva de risco_relativo. 
    
    Modifiquei os tipos de dados das variáveis ultimo_salario, linhas_de_credito_nao_garantidas_por_ativos_pessoais e indice_endividamento para integer.
    
    ---
    
    Analisei as variáveis numéricas e a única variável que apresentou uma correlação foi entre a quantidade_emprestimos e emprestimos_outros, as demais variáveis apresentaram correlação nula.
    
    ---
    
    Descrição das variáveis que foram padronizadas para português e das novas variáveis feitas para realizar a análise.
    
    Eu optei por não utilizar a variável sexo para realizar o score por compreender que essa variável poderia deixar o modelo tendencioso.
    
    Foram foi feitas algumas variáveis que acabaram não sendo necessárias para a análise como os quartis.
    
    - Variáveis do banco de dados
        1. user_id (id_usuario) - Número de identificação do cliente (único para cada cliente) [INTEGER];
        2. age (idade) - Idade do cliente [INTEGER];
        3. faixa_etaria - A idade dos clientes do banco foi dividida em 5 faixas etárias de 21-30, de 31-40, de 41-50, de 51-60 e 61+. [STRING];
        4. sex (sexo) - Gênero do cliente;
        5. default_flag (indicador_inadimplencia) - Classificação dos clientes inadimplentes (1 para clientes já registrados alguma vez como inadimplentes, 0 para clientes sem histórico de inadimplência) [INTEGER];
        6. quantidade_emprestimos - O total de emprestimos imobiliários e outros realizado pelos clientes [INTEGER];
        7. emprestimos_imobiliarios - O total de emprestimos imobiliários realizado pelos clientes [INTEGER];
        8. emprestimos_outros - O total de emprestimos do tipo outros realizado pelos clientes [INTEGER];
        9. tipos_emprestimos - Classificação dos tipos de emprestimos realizado pelos clientes dividido em imobiliários, outros, imobiliários/outros e sem registro [STRING];
        10. last_month_salary (ultimo_salario) - Último salário mensal que o cliente informou ao banco [INTEGER];
        11. **more_90_days_overdue  (atraso_superior_90_dias) - Número de vezes que o cliente apresentou atraso superior a 90 dias [INTEGER];**
        12. **using_lines_not_secured_personal_assets (linhas_de_credito_nao_garantidas_por_ativos_pessoais) -  Quanto o cliente está utilizando em relação ao seu limite de crédito, em linhas que não são garantidas por bens pessoais, como imóveis e automóveis [INTEGER];**
        13. **debt_ratio (indice_endividamento) - Relação entre dívidas e ativos do cliente. Taxa de endividamento = Dívidas / Patrimonio [INTEGER];**
        14. **number_dependents (numero_dependentes) - Número de dependentes [INTEGER];**
        15. idade_risco - Todas os valores de idade em que o indicador de inadimplência era 1 dividido pelo valor total das idades e arredondado [FLOAT];
        16. emprestimo_risco - Todas os valores da quantidade de emprestimos realizado pelos clientes em que o indicador de inadimplência era 1 dividido pelo valor total da quantidade de emprestimos realizados e arredondado [FLOAT];
        17. salario_risco -  Todas os valores do último salário em que o indicador de inadimplência era 1 dividido pelo valor total de todos os últimos salários e arredondado [FLOAT];
        18. atraso_risco - Todas os valores de atraso superior a 90 dias em que o indicador de inadimplência era 1 dividido pelo valor total de atrasos superior a 90 dias e arredondado [FLOAT];
        19. endividamento_risco - Todas os valores do índice de endividamento em que o indicador de inadimplência era 1 dividido pelo valor total do índice de endividamento e arredondado [FLOAT];
        20. credito_risco - Todas os valores de linhas de crédito não garantidas por ativos pessoais em que o indicador de inadimplência era 1 dividido pelo valor total das  linhas de crédito não garantidas por ativos pessoais e arredondado [FLOAT];
        21. genero_binario - Os valores 1 são clientes do sexo feminino e os valores 0 são dos clientes do sexo masculino [INTEGER];
        22. idade_binario - Os valores do risco relativo de idade dividido em dois valores (0 e 1) conforme a metade dos valores obtidos [INTEGER];
        23. emprestimo_binario - Os valores do risco relativo da quantidade de emprestimos dividido em dois valores (0 e 1) conforme a metade dos valores obtidos [INTEGER];
        24. salario_binario - Os valores do risco relativo do último salário dividido em dois valores (0 e 1) conforme a metade dos valores obtidos [INTEGER];
        25. atraso_binario - Os valores do risco relativo dos atrasos superior a 90 dias dividido em dois valores (0 e 1) conforme a metade dos valores obtidos [INTEGER];
        26. endividamento_binario - Os valores do risco relativo do índice de endividamento dividido em dois valores (0 e 1) conforme a metade dos valores obtidos [INTEGER];
        27. credito_binario - Os valores do risco relativo das linhas de crédito não garantidas por ativos pessoais dividido em dois valores (0 e 1) conforme a metade dos valores obtidos [INTEGER];
        28. dependentes_quartil_binario -  Os  quartis 1 e 2 representam o valor 0 e os quartis 3 e 4 representam o valor 1 [INTEGER];
        29. total - A soma de cada variável em binário [INTEGER];
        30. total_binario - O score da soma de variáveis binárias dividida em valores menores que 3 igual a 0 e valores maiores que 3 igual a 1, sendo o valor máximo o 7 [INTEGER].
    
    ---
    
    A matriz de confusão e a regressão logística foi realizada com dados desbalanceados e com dados balanceados. Optei por fazer o modelo com as variáveis indicador_inadimplencia e total_binario, sendo a variável dependente o indicador_inadimplencia e a independente o total_binario. 
    
    Foram feitos alguns gráficos para melhor visualização dos resultados e estes gráficos escolhidos foram plotados tanto nos dados desbalanceados como nos balanceados.
    
    Optei por balancear os dados com o SMOTE e achei satisfatório os resultados balanceados.
    
    Os gráficos utilizados foram a matriz de confusão,  curva de ROC e curva de probabilidade.
    
    ---
    
    - Big query
        - Dados nulos
            
            Query de todos os dados nulos:
            
            ```sql
            SELECT 
             COUNT(*) AS nulos
             FROM `risco-relativo-super-caja.Risco_relativo.default` 
             WHERE
             user_id is null
             or
             default_flag is null;
            
            SELECT
             COUNT(*) AS nulos
             FROM `risco-relativo-super-caja.Risco_relativo.loans_detail`
             WHERE
             user_id is null
             or
             more_90_days_overdue is null
             or
             using_lines_not_secured_personal_assets is null
             or
             number_times_delayed_payment_loan_30_59_days is null
             or
             debt_ratio is null
             or
             number_times_delayed_payment_loan_60_89_days is null;
            
            SELECT
             COUNT(*) AS nulos
             FROM `risco-relativo-super-caja.Risco_relativo.loans_outstanding`
             WHERE
             user_id is null
             or
             loan_id is null
             or
             loan_type is null;
            
            SELECT
             COUNT(*) AS nulos
             FROM `risco-relativo-super-caja.Risco_relativo.user_info`
             WHERE
             user_id is null
             or
             age is null
             or
             sex is null
             or
             last_month_salary is null
             or
             number_dependents is null;
            
             SELECT
             COUNT(*) AS salario_nulos
             FROM `risco-relativo-super-caja.Risco_relativo.user_info`
             WHERE
             last_month_salary is null;
            
             SELECT
             COUNT(*) AS dependentes_nulos
             FROM `risco-relativo-super-caja.Risco_relativo.user_info`
             WHERE
             number_dependents is null;
            ```
            
            Obtendo a mediana das colunas e aplicando nos nulos:
            
            ```sql
            SELECT
             PERCENTILE_CONT(last_month_salary, 0.5) OVER() AS mediana_salario,
             PERCENTILE_CONT(number_dependents, 0.5) OVER() AS mediana_dependentes
             FROM `risco-relativo-super-caja.Risco_relativo.user_info` LIMIT 1;
            
             SELECT
             user_id,
             age,
             sex,
             IFNULL(last_month_salary, 5400.0),
             IFNULL(number_dependents, 0)
             FROM `risco-relativo-super-caja.Risco_relativo.user_info`;
            ```
            
        - Dados duplicados
            
            Consulta para verificar dados duplicados:
            
            ```sql
            SELECT 
             user_id,
             default_flag,
             COUNT(*) AS quantidade_duplicada_default
             FROM  `risco-relativo-super-caja.Risco_relativo.default`
             GROUP BY 
             user_id, 
             default_flag
             HAVING COUNT(*) > 1;
            
            SELECT
             user_id,
             more_90_days_overdue,
             using_lines_not_secured_personal_assets,
             number_times_delayed_payment_loan_30_59_days,
             debt_ratio,
             number_times_delayed_payment_loan_60_89_days,
             COUNT(*) AS quantidade_duplicadas_detail
             FROM `risco-relativo-super-caja.Risco_relativo.loans_detail`
             GROUP BY 
             user_id,
             more_90_days_overdue,
             using_lines_not_secured_personal_assets,
             number_times_delayed_payment_loan_30_59_days,
             debt_ratio,
             number_times_delayed_payment_loan_60_89_days
             HAVING COUNT(*) > 1;
            
            SELECT
             user_id,
             loan_id,
             loan_type,
             COUNT(*) AS quantidade_duplicadas_outstanding
             FROM `risco-relativo-super-caja.Risco_relativo.loans_outstanding`
             GROUP BY
             user_id,
             loan_id,
             loan_type
             HAVING COUNT(*) > 1;
            
            SELECT
             user_id,
             age,
             sex,
             last_month_salary,
             number_dependents,
             COUNT(*) AS quantidade_duplicadas_user
             FROM `risco-relativo-super-caja.Risco_relativo.user_info`
             GROUP BY
             user_id,
             age,
             sex,
             last_month_salary,
             number_dependents
             HAVING COUNT(*) > 1; 
            ```
            
        - Tabelas auxiliares
            
            Identificando clientes questionáveis:
            
            ```sql
            WITH clientes_questionaveis AS (
             SELECT
               D.user_id,
               (CASE 
                    WHEN  D.default_flag = 1 AND U.last_month_salary IS NULL THEN 1
                    ELSE 0
                END) AS clientes_duvidosos,
              FROM `risco-relativo-super-caja.Risco_relativo.default` AS D
            
            LEFT JOIN
            
            `risco-relativo-super-caja.Risco_relativo.user_info` AS U
            ON D.user_id = U.user_id
            )
            
            SELECT
             *
             FROM clientes_questionaveis
             WHERE
             clientes_duvidosos = 1
             ORDER BY
             user_id,
             clientes_duvidosos;
            
            SELECT
             default_flag,
             COUNT(*) AS quantidade_red_flag
             FROM `risco-relativo-super-caja.Risco_relativo.default`
             GROUP BY
             default_flag
             ORDER BY
             quantidade_red_flag DESC;
            ```
            
        - Dados fora de escopo
            
            Correlação e o desvio padrão das variáveis de atraso de dívida:
            
            ```sql
            SELECT 
             ROUND(CORR(more_90_days_overdue, number_times_delayed_payment_loan_30_59_days), 2) AS _90_x_30,
             ROUND(CORR(more_90_days_overdue,number_times_delayed_payment_loan_60_89_days), 2) AS _90_x_60,
             ROUND(CORR(number_times_delayed_payment_loan_30_59_days, number_times_delayed_payment_loan_60_89_days), 2) AS _30_x_60,
              FROM `risco-relativo-super-caja.Risco_relativo.loans_detail`;
            
            SELECT
             ROUND(STDDEV(more_90_days_overdue), 2) AS _90_dias,
             ROUND(STDDEV(number_times_delayed_payment_loan_60_89_days), 2) AS _60_dias
             FROM `risco-relativo-super-caja.Risco_relativo.loans_detail` ; 
            ```
            
            - União de default e loan_outstanding
                
                Consulta padronizada e com as novas variáveis :
                
                ```sql
                WITH resumo_emprestimos AS (
                  SELECT
                    user_id AS id_usuario,
                    COUNT(loan_id) AS quantidade_emprestimos,
                    COUNT(CASE 
                            WHEN REGEXP_REPLACE(loan_type, r'^(real estate|REAL ESTATE|Real Estate)$', 'Imobiliários') = 'Imobiliários' 
                            THEN 1 
                            ELSE NULL 
                          END) AS emprestimos_imobiliarios,
                    COUNT(CASE 
                            WHEN REGEXP_REPLACE(loan_type, r'^(other|OTHER|Other|others)$', 'Outros') = 'Outros' 
                            THEN 1 
                            ELSE NULL 
                          END) AS emprestimos_outros
                  FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_outstanding`
                  GROUP BY
                    id_usuario
                )
                SELECT
                  D.user_id AS id_usuario,
                  D.default_flag AS indicador_inadimplencia,
                  COALESCE(R.quantidade_emprestimos, 0) AS quantidade_emprestimos,
                  COALESCE(R.emprestimos_imobiliarios, 0) AS emprestimos_imobiliarios,
                  COALESCE(R.emprestimos_outros, 0) AS emprestimos_outros
                FROM
                  `risco-relativo-super-caja.Risco_relativo.default` D
                LEFT JOIN
                  resumo_emprestimos R
                ON
                  D.user_id = R.id_usuario
                
                ```
                
        - Variáveis numéricas discrepantes
            - Regra de três de desvio padrão
                
                Consulta de todas variáveis com potencial de outlier:
                
                ```sql
                WITH informacao_usuario AS( 
                 SELECT
                 user_id,
                 age,
                 sex,
                 IFNULL(last_month_salary, 5400.0) AS ultimo_salario,
                 IFNULL(number_dependents, 0) AS numero_dependentes
                 FROM `risco-relativo-super-caja.Risco_relativo.user_info`
                ), 
                
                buscando_outliers AS (
                  SELECT
                    AVG(L.using_lines_not_secured_personal_assets) AS media_ativos_pessoais,
                    STDDEV(L.using_lines_not_secured_personal_assets) AS desvio_padrao_ativos_pessoais,
                    MIN(L.using_lines_not_secured_personal_assets) AS minimo_ativos_pessoais,
                    MAX(L.using_lines_not_secured_personal_assets) AS maximo_ativos_pessoais,
                    AVG(L.debt_ratio) AS media_endividamento,
                    STDDEV(L.debt_ratio) AS desvio_padrao_endividamento,
                    MIN(L.debt_ratio) AS minimo_endividamento,
                    MAX(L.debt_ratio) AS maximo_endividamento,
                    AVG(I.ultimo_salario) AS media_salario,
                    STDDEV(I.ultimo_salario) AS desvio_padrao_salario,
                    MIN(I.ultimo_salario) AS minimo_salario,
                    MAX(I.ultimo_salario) AS maximo_salario
                  FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_detail` AS L
                  
                  LEFT JOIN
                
                    informacao_usuario AS I
                    ON L.user_id = I.user_id
                ),
                
                limites AS (
                  SELECT
                    media_ativos_pessoais,
                    desvio_padrao_ativos_pessoais,
                    minimo_ativos_pessoais,
                    maximo_ativos_pessoais,
                    media_ativos_pessoais - 3 * desvio_padrao_ativos_pessoais AS limite_inferior_ativos_pessoais,
                    media_ativos_pessoais + 3 * desvio_padrao_ativos_pessoais AS limite_superior_ativos_pessoais,
                    media_endividamento,
                    desvio_padrao_endividamento,
                    minimo_endividamento,
                    maximo_endividamento,
                    media_endividamento - 3 * desvio_padrao_endividamento AS limite_inferior_endividamento,
                    media_endividamento + 3 * desvio_padrao_endividamento AS limite_superior_endividamento,
                    media_salario,
                    desvio_padrao_salario,
                    minimo_salario,
                    maximo_salario,
                    media_salario - 3 * desvio_padrao_salario AS limite_inferior_salario,
                    media_salario + 3 * desvio_padrao_salario AS limite_superior_salario
                  FROM
                   buscando_outliers 
                )
                
                SELECT
                  *
                FROM (
                    SELECT
                    L.*,
                    I.*,
                    CASE
                      WHEN L.using_lines_not_secured_personal_assets < T.limite_inferior_ativos_pessoais THEN 'Outlier'
                      WHEN L.using_lines_not_secured_personal_assets > T.limite_superior_ativos_pessoais THEN 'Outlier'
                      WHEN L.debt_ratio < T.limite_inferior_endividamento THEN 'Outlier'
                      WHEN L.debt_ratio > T.limite_superior_endividamento THEN 'Outlier'
                      WHEN I.ultimo_salario < T.limite_inferior_salario THEN 'Outlier'
                      WHEN I.ultimo_salario > T.limite_superior_salario THEN 'Outlier'
                      ELSE 'Normal'
                    END AS status
                  FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_detail` AS L
                  LEFT JOIN
                    informacao_usuario AS I
                    ON L.user_id = I.user_id
                  CROSS JOIN 
                    limites AS T
                ) AS dados
                WHERE
                  dados.status = 'Outlier';
                
                ```
                
                Consulta das variáveis individualmente:
                
                ```sql
                WITH buscando_outliers_endividamento AS (
                  SELECT
                    AVG(debt_ratio) AS media_endividamento,
                    STDDEV(debt_ratio) AS desvio_padrao_endividamento,
                    MIN(debt_ratio) AS minimo_endividamento,
                    MAX(debt_ratio) AS maximo_endividamento
                  FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_detail`
                ),
                
                limites AS (
                  SELECT
                    media_endividamento,
                    desvio_padrao_endividamento,
                    minimo_endividamento,
                    maximo_endividamento,
                    media_endividamento - 3 * desvio_padrao_endividamento AS limite_inferior_endividamento,
                    media_endividamento + 3 * desvio_padrao_endividamento AS limite_superior_endividamento,
                  FROM
                    buscando_outliers_endividamento
                )
                SELECT
                *
                FROM (
                SELECT
                  D.*,
                  CASE
                    WHEN D.debt_ratio < L.limite_inferior_endividamento THEN 'Outlier'
                    WHEN D.debt_ratio > L.limite_superior_endividamento THEN 'Outlier'
                    ELSE 'Normal'
                  END AS status_endividamento
                FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_detail` AS D
                CROSS JOIN
                  limites L
                ) AS dados
                
                WHERE
                
                dados.status_endividamento = 'Outlier';
                
                WITH buscando_outliers_ativos_pessoais AS (
                  SELECT
                    AVG(using_lines_not_secured_personal_assets) AS media_ativos_pessoais,
                    STDDEV(using_lines_not_secured_personal_assets) AS desvio_padrao_ativos_pessoais,
                    MIN(using_lines_not_secured_personal_assets) AS minimo_ativos_pessoais,
                    MAX(using_lines_not_secured_personal_assets) AS maximo_ativos_pessoais,
                  FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_detail`
                ),
                
                limites AS (
                  SELECT
                    media_ativos_pessoais,
                    desvio_padrao_ativos_pessoais,
                    minimo_ativos_pessoais,
                    maximo_ativos_pessoais,
                    media_ativos_pessoais - 3 * desvio_padrao_ativos_pessoais AS limite_inferior_ativos_pessoais,
                    media_ativos_pessoais + 3 * desvio_padrao_ativos_pessoais AS limite_superior_ativos_pessoais,
                    FROM
                    buscando_outliers_ativos_pessoais
                )
                SELECT
                *
                FROM (
                SELECT
                  D.*,
                  CASE
                    WHEN D.using_lines_not_secured_personal_assets < L.limite_inferior_ativos_pessoais THEN 'Outlier'
                    WHEN D.using_lines_not_secured_personal_assets > L.limite_superior_ativos_pessoais THEN 'Outlier'
                    ELSE 'Normal'
                  END AS status_ativos_pessoais
                FROM
                    `risco-relativo-super-caja.Risco_relativo.loans_detail` AS D
                CROSS JOIN
                  limites L
                ) AS dados
                
                WHERE
                
                dados.status_ativos_pessoais = 'Outlier';
                
                WITH informacao_usuario AS( 
                 SELECT
                 user_id,
                 age,
                 sex,
                 IFNULL(last_month_salary, 5400.0) AS ultimo_salario,
                 IFNULL(number_dependents, 0) AS numero_dependentes
                 FROM `risco-relativo-super-caja.Risco_relativo.user_info`
                ),
                
                buscando_outliers_informacao_usuario AS (
                  SELECT
                    AVG(ultimo_salario) AS media_salario,
                    STDDEV(ultimo_salario) AS desvio_padrao_salario,
                    MIN(ultimo_salario) AS minimo_salario,
                    MAX(ultimo_salario) AS maximo_salario,
                  FROM
                 informacao_usuario
                ),
                
                limites AS (
                  SELECT
                    media_salario,
                    desvio_padrao_salario,
                    minimo_salario,
                    maximo_salario,
                    media_salario - 3 * desvio_padrao_salario AS limite_inferior_salario,
                    media_salario + 3 * desvio_padrao_salario AS limite_superior_salario,
                    FROM
                    buscando_outliers_informacao_usuario
                )
                SELECT
                *
                FROM (
                SELECT
                 I.*,
                  CASE
                    WHEN I.ultimo_salario < L.limite_inferior_salario THEN 'Outlier'
                    WHEN I.ultimo_salario > L.limite_superior_salario THEN 'Outlier'
                    ELSE 'Normal'
                  END AS status_salario
                FROM
                    informacao_usuario AS I
                CROSS JOIN
                  limites L
                ) AS dados
                
                WHERE
                
                dados.status_salario = 'Outlier';
                ```
                
        - Variáveis categoricas discrepantes
            
            Mudança de strings para outros termos:
            
            ```sql
            SELECT 
             loan_type
             FROM `risco-relativo-super-caja.Risco_relativo.loans_outstanding` 
             GROUP BY
             loan_type;
            
            SELECT
              REGEXP_REPLACE(
                REGEXP_REPLACE(loan_type, r'^(real estate|REAL ESTATE|Real Estate)$', 'Imobiliários'),
                r'^(other|OTHER|Other|others)$', 'Outros'
              ) AS tipos_emprestimos
            FROM
              `risco-relativo-super-caja.Risco_relativo.loans_outstanding`
              GROUP BY
              tipos_emprestimos;
            
            SELECT 
             sex
             FROM `risco-relativo-super-caja.Risco_relativo.user_info` 
             GROUP BY
             sex;
            
            SELECT
              REGEXP_REPLACE(
                REGEXP_REPLACE(sex, r'^(F)$', 'Feminino'),
                r'^(M)$', 'Masculino'
              ) AS sexo
            FROM
              `risco-relativo-super-caja.Risco_relativo.user_info`
              GROUP BY
              sexo; 
            ```
            
        - Alterando outliers de último salário
            
            Alteração desses dados selecionados:
            
            ```sql
            WITH dados_winsorizados AS (
              SELECT
                ultimo_salario,
                PERCENTILE_CONT(ultimo_salario, 0.05) OVER () AS limite_inferior,
                PERCENTILE_CONT(ultimo_salario, 0.95) OVER () AS limite_superior,
                PERCENTILE_CONT(ultimo_salario, 0.5) OVER() AS mediana_salario
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo_outliers`
             )
            SELECT
            ultimo_salario_winsorizado
            FROM (
            SELECT
             *, 
              CASE
                WHEN ultimo_salario < limite_inferior THEN mediana_salario
                WHEN ultimo_salario > limite_superior THEN mediana_salario
                ELSE ultimo_salario
              END AS ultimo_salario_winsorizado,
            FROM
              dados_winsorizados
            )
            ```
            
            Contagem de quantos dados foram alterados e obtendo a média e desvio padrão dos  dados alterados:
            
            ```sql
            WITH dados_winsorizados AS (
              SELECT
                ultimo_salario,
                AVG(ultimo_salario) OVER() AS media_salario,
                PERCENTILE_CONT(ultimo_salario, 0.05) OVER () AS limite_inferior,
                PERCENTILE_CONT(ultimo_salario, 0.95) OVER () AS limite_superior
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo_outliers`
            ),
            dados_modificados AS (
              SELECT
                *,
                CASE
                  WHEN ultimo_salario < limite_inferior THEN media_salario
                  WHEN ultimo_salario > limite_superior THEN media_salario
                  ELSE ultimo_salario
                END AS ultimo_salario_winsorizado,
                CASE
                  WHEN ultimo_salario < limite_inferior THEN 1
                  WHEN ultimo_salario > limite_superior THEN 1
                  ELSE 0
                END AS salarios_modificados
              FROM
                dados_winsorizados
            )
            SELECT
              ROUND(AVG(ultimo_salario_winsorizado)) AS media_salario,
              ROUND(STDDEV(ultimo_salario_winsorizado)) AS desvio_padro_salario,
              COUNTIF(salarios_modificados = 1) AS total_modificados
            FROM
              dados_modificados;
            ```
            
        - União das tabelas com os outliers
            
            Uni as tabelas com os dados da tabela loan_outstanding sem ultrapassar os valores dos dados:
            
            ```sql
            ----CREATE TABLE `risco-relativo-super-caja.Risco_relativo.risco_relativo_outliers` AS
            WITH resumo_emprestimos AS (
              SELECT
                user_id AS id_usuario,
                COUNT(user_id) AS quantidade_emprestimos,
                SUM(CASE 
                      WHEN REGEXP_REPLACE(loan_type, r'^(real estate|REAL ESTATE|Real Estate)$', 'Imobiliários') = 'Imobiliários' 
                      THEN 1 
                      ELSE 0 
                    END) AS emprestimos_imobiliarios,
                SUM(CASE 
                      WHEN REGEXP_REPLACE(loan_type, r'^(other|OTHER|Other|others)$', 'Outros') = 'Outros' 
                      THEN 1 
                      ELSE 0 
                    END) AS emprestimos_outros
              FROM
                `risco-relativo-super-caja.Risco_relativo.loans_outstanding`
              GROUP BY
                user_id
            ),
            tipos_emprestimos_rotulos AS (
              SELECT
                id_usuario,
                quantidade_emprestimos,
                emprestimos_imobiliarios,
                emprestimos_outros,
                CASE 
                  WHEN emprestimos_imobiliarios > 0 AND emprestimos_outros > 0 THEN 'Imobiliários/Outros'
                  WHEN emprestimos_imobiliarios > 0 THEN 'Imobiliários'
                  WHEN emprestimos_outros > 0 THEN 'Outros'
                  ELSE 'Sem registro'
                END AS tipos_emprestimos
              FROM
                resumo_emprestimos
            )
            SELECT
              D.user_id AS id_usuario,
              U.age AS idade,
              REGEXP_REPLACE(
                REGEXP_REPLACE(sex, r'^(F)$', 'Feminino'),
                r'^(M)$', 'Masculino'
              ) AS sexo,
              D.default_flag AS indicador_inadimplencia,
              COALESCE(T.quantidade_emprestimos, 0) AS quantidade_emprestimos,
              COALESCE(T.emprestimos_imobiliarios, 0) AS emprestimos_imobiliarios,
              COALESCE(T.emprestimos_outros, 0) AS emprestimos_outros,
              COALESCE(T.tipos_emprestimos,'Sem registro') AS tipos_emprestimos,
              CAST(IFNULL(U.last_month_salary, 6675.0) AS INT64) AS ultimo_salario,
              L.more_90_days_overdue AS atraso_superior_90_dias,
              CAST(L.using_lines_not_secured_personal_assets AS INT64) AS linhas_de_credito_nao_garantidas_por_ativos_pessoais,
              CAST(L.debt_ratio AS INT64) AS indice_endividamento,
              IFNULL(U.number_dependents, 0) AS numero_dependentes
            FROM
              `risco-relativo-super-caja.Risco_relativo.default` D
            LEFT JOIN
              tipos_emprestimos_rotulos T
            ON
              D.user_id = T.id_usuario
            LEFT JOIN
              `risco-relativo-super-caja.Risco_relativo.loans_detail` L
            ON
              D.user_id = L.user_id
            LEFT JOIN
             `risco-relativo-super-caja.Risco_relativo.user_info` U
            ON
             D.user_id = U.user_id
            
            ```
            
        - Correlação entre variáveis numéricas
            
            Dados limpos e sem outliers:
            
            ```sql
            SELECT 
             ROUND(CORR(ultimo_salario,atraso_superior_90_dias)) AS salario_atraso,
             ROUND(CORR(ultimo_salario,linhas_de_credito_nao_garantidas_por_ativos_pessoais)) AS salario_credito,
             ROUND(CORR(ultimo_salario,indice_endividamento)) AS salario_endividamento,
             ROUND(CORR(ultimo_salario,indicador_inadimplencia)) AS salario_inadimplencia,
             ROUND(CORR(ultimo_salario,idade)) AS salario_idade,
             ROUND(CORR(ultimo_salario,quantidade_emprestimos)) AS salario_emprestimos,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais,indice_endividamento)) AS credito_endividamento,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais, indicador_inadimplencia)) AS credito_inadimplencia,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais, atraso_superior_90_dias)) AS credito_atraso,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais,idade)) AS credito_idade,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais,quantidade_emprestimos)) AS credito_emprestimos,
             ROUND(CORR(indice_endividamento,quantidade_emprestimos)) AS endividamento_emprestimos,
             ROUND(CORR(indice_endividamento,atraso_superior_90_dias)) AS endividamento_atraso,
             ROUND(CORR(indice_endividamento,indicador_inadimplencia)) AS endividamento_inadimplencia,
             ROUND(CORR(indicador_inadimplencia,atraso_superior_90_dias)) AS inadimplencia_atraso,
             ROUND(CORR(indicador_inadimplencia,idade)) AS inadimplencia_idade,
             ROUND(CORR(indicador_inadimplencia,quantidade_emprestimos)) AS inadimplencia_emprestimo,
             ROUND(CORR(atraso_superior_90_dias,quantidade_emprestimos)) AS atraso_emprestimos,
             ROUND(CORR(atraso_superior_90_dias,idade)) AS atraso_idade,
             ROUND(CORR(quantidade_emprestimos,idade)) AS emprestimos_idade,
             ROUND(CORR(emprestimos_imobiliarios,emprestimos_outros)) AS imobiliarios_outros,
             ROUND(CORR(emprestimos_imobiliarios,quantidade_emprestimos)) AS total_imobiliarios,
             ROUND(CORR(emprestimos_outros, quantidade_emprestimos)) AS total_outros
            FROM `risco-relativo-super-caja.Risco_relativo.risco_relativo`;
            ```
            
            Dados com outliers:
            
            ```sql
            SELECT 
             ROUND(CORR(ultimo_salario,atraso_superior_90_dias)) AS salario_atraso,
             ROUND(CORR(ultimo_salario,linhas_de_credito_nao_garantidas_por_ativos_pessoais)) AS salario_credito,
             ROUND(CORR(ultimo_salario,indice_endividamento)) AS salario_endividamento,
             ROUND(CORR(ultimo_salario,indicador_inadimplencia)) AS salario_inadimplencia,
             ROUND(CORR(ultimo_salario,idade)) AS salario_idade,
             ROUND(CORR(ultimo_salario,quantidade_emprestimos)) AS salario_emprestimos,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais,indice_endividamento)) AS credito_endividamento,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais, indicador_inadimplencia)) AS credito_inadimplencia,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais, atraso_superior_90_dias)) AS credito_atraso,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais,idade)) AS credito_idade,
             ROUND(CORR(linhas_de_credito_nao_garantidas_por_ativos_pessoais,quantidade_emprestimos)) AS credito_emprestimos,
             ROUND(CORR(indice_endividamento,quantidade_emprestimos)) AS endividamento_emprestimos,
             ROUND(CORR(indice_endividamento,atraso_superior_90_dias)) AS endividamento_atraso,
             ROUND(CORR(indice_endividamento,indicador_inadimplencia)) AS endividamento_inadimplencia,
             ROUND(CORR(indicador_inadimplencia,atraso_superior_90_dias)) AS inadimplencia_atraso,
             ROUND(CORR(indicador_inadimplencia,idade)) AS inadimplencia_idade,
             ROUND(CORR(indicador_inadimplencia,quantidade_emprestimos)) AS inadimplencia_emprestimo,
             ROUND(CORR(atraso_superior_90_dias,quantidade_emprestimos)) AS atraso_emprestimos,
             ROUND(CORR(atraso_superior_90_dias,idade)) AS atraso_idade,
             ROUND(CORR(quantidade_emprestimos,idade)) AS emprestimos_idade
            FROM `risco-relativo-super-caja.Risco_relativo.risco_relativo_outliers`
            ```
            
        - Tabela definitiva de risco_relativo
            
            Dados reduzidos e com variáveis binárias:
            
            ```sql
            --CREATE OR REPLACE TABLE `risco-relativo-super-caja.Risco_relativo.risco_relativo` AS
            SELECT
            id_usuario,
            idade,
            faixa_etaria,
            sexo,
            indicador_inadimplencia,
            quantidade_emprestimos,
            emprestimos_imobiliarios,
            emprestimos_outros,
            tipos_emprestimos,
            ultimo_salario,
            atraso_superior_90_dias,
            linhas_de_credito_nao_garantidas_por_ativos_pessoais,
            indice_endividamento,
            numero_dependentes,
            idade_risco,
            emprestimo_risco,
            salario_risco,
            atraso_risco,
            endividamento_risco,
            credito_risco,
            CASE 
            WHEN idade_risco > 0.03 THEN 1 ELSE 0 
            END AS idade_binario,
            CASE 
            WHEN emprestimo_risco > 0.03 THEN 1 ELSE 0 
            END AS emprestimo_binario,
            CASE 
            WHEN salario_risco > 0.10 THEN 1 ELSE 0 
            END AS salario_binario,
            CASE 
            WHEN atraso_risco > 0.66 THEN 1 ELSE 0 
            END AS atraso_binario,
            CASE 
            WHEN endividamento_risco > 0.10 THEN 1 ELSE 0 
            END AS endividamento_binario,
            CASE WHEN credito_risco = 0.07 THEN 1 ELSE 0 
            END AS credito_binario,
            CASE WHEN sexo = 'Feminino' THEN 1 ELSE 0 
            END AS genero_binario,
            CASE 
            WHEN dependentes_quartil IN (1, 2) THEN 0 ELSE 1 
            END AS dependentes_quartil_binario 
            FROM
            `risco-relativo-super-caja.Risco_relativo.risco_relativo_01`
            ```
            
            Dados com outliers:
            
            ```sql
            ----CREATE TABLE `risco-relativo-super-caja.Risco_relativo.risco_relativo_outliers` AS
            WITH resumo_emprestimos AS (
              SELECT
                user_id AS id_usuario,
                COUNT(user_id) AS quantidade_emprestimos,
                SUM(CASE 
                      WHEN REGEXP_REPLACE(loan_type, r'^(real estate|REAL ESTATE|Real Estate)$', 'Imobiliários') = 'Imobiliários' 
                      THEN 1 
                      ELSE 0 
                    END) AS emprestimos_imobiliarios,
                SUM(CASE 
                      WHEN REGEXP_REPLACE(loan_type, r'^(other|OTHER|Other|others)$', 'Outros') = 'Outros' 
                      THEN 1 
                      ELSE 0 
                    END) AS emprestimos_outros
              FROM
                `risco-relativo-super-caja.Risco_relativo.loans_outstanding`
              GROUP BY
                user_id
            ),
            tipos_emprestimos_rotulos AS (
              SELECT
                id_usuario,
                quantidade_emprestimos,
                emprestimos_imobiliarios,
                emprestimos_outros,
                CASE 
                  WHEN emprestimos_imobiliarios > 0 AND emprestimos_outros > 0 THEN 'Imobiliários/Outros'
                  WHEN emprestimos_imobiliarios > 0 THEN 'Imobiliários'
                  WHEN emprestimos_outros > 0 THEN 'Outros'
                  ELSE 'Sem registro'
                END AS tipos_emprestimos
              FROM
                resumo_emprestimos
            )
            SELECT
              D.user_id AS id_usuario,
              U.age AS idade,
              REGEXP_REPLACE(
                REGEXP_REPLACE(sex, r'^(F)$', 'Feminino'),
                r'^(M)$', 'Masculino'
              ) AS sexo,
              D.default_flag AS indicador_inadimplencia,
              COALESCE(T.quantidade_emprestimos, 0) AS quantidade_emprestimos,
              COALESCE(T.emprestimos_imobiliarios, 0) AS emprestimos_imobiliarios,
              COALESCE(T.emprestimos_outros, 0) AS emprestimos_outros,
              COALESCE(T.tipos_emprestimos,'Sem registro') AS tipos_emprestimos,
              CAST(IFNULL(U.last_month_salary, 6675.0) AS INT64) AS ultimo_salario,
              L.more_90_days_overdue AS atraso_superior_90_dias,
              CAST(L.using_lines_not_secured_personal_assets AS INT64) AS linhas_de_credito_nao_garantidas_por_ativos_pessoais,
              CAST(L.debt_ratio AS INT64) AS indice_endividamento,
              IFNULL(U.number_dependents, 0) AS numero_dependentes
            FROM
              `risco-relativo-super-caja.Risco_relativo.default` D
            LEFT JOIN
              tipos_emprestimos_rotulos T
            ON
              D.user_id = T.id_usuario
            LEFT JOIN
              `risco-relativo-super-caja.Risco_relativo.loans_detail` L
            ON
              D.user_id = L.user_id
            LEFT JOIN
             `risco-relativo-super-caja.Risco_relativo.user_info` U
            ON
             D.user_id = U.user_id
            ```
            
            Dados com quartis:
            
            ```sql
            ---CREATE OR REPLACE TABLE `risco-relativo-super-caja.Risco_relativo.risco_relativo_quartis` AS
            WITH dados AS (
              SELECT
                id_usuario,
                idade,
                sexo,
                tipos_emprestimos,
                emprestimos_imobiliarios,
                emprestimos_outros,
                quantidade_emprestimos,
                ultimo_salario,
                linhas_de_credito_nao_garantidas_por_ativos_pessoais,
                indice_endividamento,
                indicador_inadimplencia,
                atraso_superior_90_dias,
                numero_dependentes,
                PERCENTILE_CONT(ultimo_salario, 0.05) OVER () AS limite_inferior_salario,
                PERCENTILE_CONT(ultimo_salario, 0.95) OVER () AS limite_superior_salario,
                PERCENTILE_CONT(ultimo_salario, 0.5) OVER () AS mediana_salario,
                PERCENTILE_CONT(linhas_de_credito_nao_garantidas_por_ativos_pessoais, 0.05) OVER () AS limite_inferior_credito,
                PERCENTILE_CONT(linhas_de_credito_nao_garantidas_por_ativos_pessoais, 0.95) OVER () AS limite_superior_credito,
                PERCENTILE_CONT(linhas_de_credito_nao_garantidas_por_ativos_pessoais, 0.5) OVER () AS mediana_credito,
                PERCENTILE_CONT(indice_endividamento, 0.05) OVER () AS limite_inferior_endividamento,
                PERCENTILE_CONT(indice_endividamento, 0.95) OVER () AS limite_superior_endividamento,
                PERCENTILE_CONT(indice_endividamento, 0.5) OVER () AS mediana_endividamento,
                NTILE(4) OVER (ORDER BY idade) AS idade_quartil,
                NTILE(4) OVER (ORDER BY ultimo_salario) AS salario_quartil,
                NTILE(4) OVER (ORDER BY linhas_de_credito_nao_garantidas_por_ativos_pessoais) AS credito_quartil,
                NTILE(4) OVER (ORDER BY emprestimos_imobiliarios) AS imobiliarios_quartil,
                NTILE(4) OVER (ORDER BY emprestimos_outros) AS outros_quartil,
                NTILE(4) OVER (ORDER BY quantidade_emprestimos) AS quantidade_quartil,
                NTILE(4) OVER (ORDER BY indice_endividamento) AS endividamento_quartil,
               NTILE(4) OVER (ORDER BY atraso_superior_90_dias) AS atraso_quartil,
               NTILE(4) OVER (ORDER BY numero_dependentes) AS dependentes_quartil
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo_outliers`
            )
            
            SELECT
              id_usuario,
              idade,
              CASE
                WHEN idade BETWEEN 21 AND 30 THEN '21-30'
                WHEN idade BETWEEN 31 AND 40 THEN '31-40'
                WHEN idade BETWEEN 41 AND 50 THEN '41-50'
                WHEN idade BETWEEN 51 AND 60 THEN '51-60'
                ELSE '61+'
              END AS faixa_etaria,
              sexo,
              tipos_emprestimos,
              emprestimos_imobiliarios,
              emprestimos_outros,
              quantidade_emprestimos,
              CAST(CASE
                WHEN ultimo_salario < limite_inferior_salario THEN mediana_salario
                WHEN ultimo_salario > limite_superior_salario THEN mediana_salario
                ELSE ultimo_salario
              END AS INT64) AS ultimo_salario,
              CAST(CASE
                WHEN linhas_de_credito_nao_garantidas_por_ativos_pessoais < limite_inferior_credito THEN limite_inferior_credito
                WHEN linhas_de_credito_nao_garantidas_por_ativos_pessoais > limite_superior_credito THEN limite_superior_credito
                ELSE linhas_de_credito_nao_garantidas_por_ativos_pessoais
              END AS INT64) AS linhas_de_credito_nao_garantidas_por_ativos_pessoais,
              CAST(CASE
                WHEN indice_endividamento < limite_inferior_endividamento THEN mediana_endividamento
                WHEN indice_endividamento > limite_superior_endividamento THEN mediana_endividamento
                ELSE indice_endividamento
              END AS INT64) AS indice_endividamento,
              indicador_inadimplencia,
              atraso_superior_90_dias,
              numero_dependentes,
              idade_quartil,
              CASE WHEN idade_quartil = 4 THEN "Jovens adultos"
                   WHEN idade_quartil = 3 THEN "Adultos"
                   WHEN idade_quartil = 2 THEN "Adultos sêniores"
                   ELSE "Idosos" END AS idade_grupo,
              salario_quartil,
              CASE WHEN salario_quartil = 4 THEN "Grupo Vermelho"
                   WHEN salario_quartil = 3 THEN "Grupo Amarelo"
                   WHEN salario_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS salario_grupo,
              credito_quartil,
              CASE WHEN credito_quartil = 4 THEN "Grupo Vermelho"
                   WHEN credito_quartil = 3 THEN "Grupo Amarelo"
                   WHEN credito_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS credito_grupo,
              imobiliarios_quartil,
              CASE WHEN imobiliarios_quartil = 4 THEN "Grupo Vermelho"
                   WHEN imobiliarios_quartil = 3 THEN "Grupo Amarelo"
                   WHEN imobiliarios_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS imobiliarios_grupo,
              outros_quartil,
              CASE WHEN outros_quartil = 4 THEN "Grupo Vermelho"
                   WHEN outros_quartil = 3 THEN "Grupo Amarelo"
                   WHEN outros_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS outros_grupo,
              quantidade_quartil,
              CASE WHEN quantidade_quartil = 4 THEN "Grupo Vermelho"
                   WHEN quantidade_quartil = 3 THEN "Grupo Amarelo"
                   WHEN quantidade_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS quantidade_grupo,
              endividamento_quartil,
              CASE WHEN endividamento_quartil = 4 THEN "Grupo Vermelho"
                   WHEN endividamento_quartil = 3 THEN "Grupo Amarelo"
                   WHEN endividamento_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS endividamento_grupo,
              atraso_quartil,
              CASE WHEN atraso_quartil = 4 THEN "Grupo Vermelho"
                   WHEN atraso_quartil = 3 THEN "Grupo Amarelo"
                   WHEN atraso_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS atraso_grupo,
              dependentes_quartil,
              CASE WHEN dependentes_quartil = 4 THEN "Grupo Vermelho"
                   WHEN dependentes_quartil = 3 THEN "Grupo Amarelo"
                   WHEN dependentes_quartil = 2 THEN "Grupo Azul"
                   ELSE "Grupo Verde" END AS dependentes_grupo
            FROM
              dados;
            
            ```
            
            Dados alterados e unidos:
            
            ```sql
            --CREATE OR REPLACE TABLE `risco-relativo-super-caja.Risco_relativo.risco_relativo` AS
            WITH idade_stats AS (
              SELECT
                idade,
                COUNT(idade) AS total_idade,
                SUM(CASE WHEN indicador_inadimplencia = 1 THEN 1 ELSE 0 END) AS inadimplencia_total_idade,
                'idade' AS idade_variavel,
                CAST(idade AS STRING) AS idade_valor
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo`
              GROUP BY
                idade
            ),
            
            emprestimos_stats AS (
              SELECT
                quantidade_emprestimos,
                COUNT(quantidade_emprestimos) AS total_quantidade_emprestimos,
                SUM(CASE WHEN indicador_inadimplencia = 1 THEN 1 ELSE 0 END) AS inadimplencia_total_emprestimos,
                'quantidade_emprestimos' AS emprestimos_variavel,
                CAST(quantidade_emprestimos AS STRING) AS emprestimos_valor
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo`
              GROUP BY
                quantidade_emprestimos
            ),
            
            salario_stats AS (
              SELECT
                ultimo_salario,
                COUNT(ultimo_salario) AS total_salario,
                SUM(CASE WHEN indicador_inadimplencia = 1 THEN 1 ELSE 0 END) AS inadimplencia_total_salario,
                'ultimo_salario' AS salario_variavel,
                CAST(ultimo_salario AS STRING) AS salario_valor
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo`
              GROUP BY
                ultimo_salario
            ),
            
            atraso_stats AS (
              SELECT
                atraso_superior_90_dias,
                COUNT(atraso_superior_90_dias) AS total_atraso,
                SUM(CASE WHEN indicador_inadimplencia = 1 THEN 1 ELSE 0 END) AS inadimplencia_total_atraso,
                'atraso_superior_90_dias' AS atraso_variavel,
                CAST(atraso_superior_90_dias AS STRING) AS atraso_valor
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo`
              GROUP BY
                atraso_superior_90_dias
            ),
            
            endividamento_stats AS (
              SELECT
                indice_endividamento,
                COUNT(indice_endividamento) AS total_endividamento,
                SUM(CASE WHEN indicador_inadimplencia = 1 THEN 1 ELSE 0 END) AS inadimplencia_total_endividamento,
                'indice_endividamento' AS endividamento_variavel,
                CAST(indice_endividamento AS STRING) AS endividamento_valor
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo`
              GROUP BY
                indice_endividamento
            ),
            
            credito_stats AS (
              SELECT
                linhas_de_credito_nao_garantidas_por_ativos_pessoais,
                COUNT(linhas_de_credito_nao_garantidas_por_ativos_pessoais) AS credito_total,
                SUM(CASE WHEN indicador_inadimplencia = 1 THEN 1 ELSE 0 END) AS inadimplencia_total_credito,
                'linhas_de_credito_nao_garantidas_por_ativos_pessoais' AS credito_variavel,
                CAST(linhas_de_credito_nao_garantidas_por_ativos_pessoais AS STRING) AS credito_valor
              FROM
                `risco-relativo-super-caja.Risco_relativo.risco_relativo`
              GROUP BY
                linhas_de_credito_nao_garantidas_por_ativos_pessoais
            )
            
            SELECT 
              R.id_usuario,
              R.idade,
              R.faixa_etaria,
              R.sexo,
              R.tipos_emprestimos,
              R.emprestimos_imobiliarios,
              R.emprestimos_outros,
              R.quantidade_emprestimos,
              R.ultimo_salario,
              R.linhas_de_credito_nao_garantidas_por_ativos_pessoais,
              R.indice_endividamento,
              R.indicador_inadimplencia,
              R.atraso_superior_90_dias,
              R.numero_dependentes,
              R.idade_quartil,
              R.idade_grupo,
              R.salario_quartil,
              R.salario_grupo,
              R.credito_quartil,
              R.credito_grupo,
              R.imobiliarios_quartil,
              R.imobiliarios_grupo,
              R.outros_quartil,
              R.outros_grupo,
              R.quantidade_quartil,
              R.quantidade_grupo,
              R.endividamento_quartil,
              R.endividamento_grupo,
              R.atraso_quartil,
              R.atraso_grupo,
              R.dependentes_quartil,
              R.dependentes_grupo,
              I.idade_variavel,
              I.idade_valor,
              I.inadimplencia_total_idade,
              I.total_idade,
              ROUND((I.inadimplencia_total_idade / I.total_idade), 2) AS idade_risco,
              Q.emprestimos_variavel,
              Q.emprestimos_valor,
              Q.inadimplencia_total_emprestimos,
              Q.total_quantidade_emprestimos,
              ROUND((Q.inadimplencia_total_emprestimos / Q.total_quantidade_emprestimos), 2) AS emprestimo_risco,
              S.salario_variavel,
              S.salario_valor,
              S.inadimplencia_total_salario,
              S.total_salario,
              ROUND((S.inadimplencia_total_salario / S.total_salario), 2) AS salario_risco,
              A.atraso_variavel,
              A.atraso_valor,
              A.inadimplencia_total_atraso,
              A.total_atraso,
              ROUND((A.inadimplencia_total_atraso / A.total_atraso), 2) AS atraso_risco,
              E.endividamento_variavel,
              E.endividamento_valor,
              E.inadimplencia_total_endividamento,
              E.total_endividamento,
              ROUND((E.inadimplencia_total_endividamento / E.total_endividamento), 2) AS endividamento_risco,
              C.credito_variavel,
              C.credito_valor,
              C.inadimplencia_total_credito,
              C.credito_total,
              ROUND((C.inadimplencia_total_credito / C.credito_total), 2) AS credito_risco,
            FROM 
             `risco-relativo-super-caja.Risco_relativo.risco_relativo` AS R
             LEFT JOIN
             idade_stats AS I
             ON
             R.idade = I.idade
             LEFT JOIN
             emprestimos_stats AS Q
             ON
             R.quantidade_emprestimos = Q.quantidade_emprestimos
            LEFT JOIN
            salario_stats AS S  
            ON
            R.ultimo_salario = S.ultimo_salario
            LEFT JOIN
            atraso_stats AS A
            ON
            R.atraso_superior_90_dias = A.atraso_superior_90_dias
            LEFT JOIN
            endividamento_stats AS E  
            ON
            R.indice_endividamento = E.indice_endividamento
            LEFT JOIN
            credito_stats AS C
            ON
            R.linhas_de_credito_nao_garantidas_por_ativos_pessoais = C.linhas_de_credito_nao_garantidas_por_ativos_pessoais
            
            ```
            
- **Resultados e Conclusões:**
    - Validação das hipóteses levantadas:
        
        • Os mais jovens correm um risco maior de não pagamento: CONFIRMADA.
        
        Os clientes que apresentam o maior risco relativo são apenas da faixa etária adultos jovens, específicamente apenas os clientes com 25 anos, que estão entre 21-30 anos.
        
        • Pessoas com mais empréstimos ativos correm maior risco de serem maus pagadores: REFUTADA.
        
        Os clientes que apresentavam maior número de empréstimos ativos não apresentavam maior risco relativo.
        
        • Pessoas que atrasaram seus pagamentos por mais de 90 dias correm maior risco de serem maus pagadores: CONFIRMADA.
        
        Os clientes que apresentaram atraso superior a 90 dias também apresentaram maior risco relativo.
        
    - Matriz de confusão e regressão logística
        
        Os resultados mostram que o modelo, mesmo sem balanceamento de classes, apresenta um bom desempenho, com uma AUC (Área sob a Curva ROC) relativamente alta. No entanto, a aplicação da técnica SMOTE para balancear as classes melhora ainda mais o desempenho do modelo, resultando em uma AUC ainda maior e um melhor equilíbrio entre as métricas de precisão e recall.
        
        O modelo de regressão logística, mesmo sem técnicas de balanceamento de classes, apresentou um bom desempenho na previsão da inadimplência, conforme indicado pela alta AUC (Área sob a Curva ROC). A aplicação da técnica SMOTE (Synthetic Minority Over-sampling Technique) para balancear as classes melhorou ainda mais o desempenho do modelo, como evidenciado pelo aumento da AUC.
        
         A utilização do SMOTE resultou em um modelo mais equilibrado, com melhor capacidade de prever tanto a classe de inadimplentes quanto a de não inadimplentes. Isso é crucial para evitar vieses na concessão de crédito e garantir decisões mais justas e precisas.
        
- **Limitações/Próximos Passos:**
    
    A falta de datas específicas dos empréstimos impede a construção de uma linha do tempo, essencial para identificar períodos mais propensos para a concessão de empréstimos. Isso limita a capacidade de detectar padrões sazonais ou tendências temporais que podem influenciar a probabilidade de inadimplência.
    
    A inclusão de dados de gênero pode introduzir vieses nos resultados da análise. Além disso, a ausência de uma abordagem inclusiva que considere todos os gêneros limita a representatividade e a precisão da análise, podendo levar a conclusões enviesadas.
    
    A ausência de dados sobre os valores dos empréstimos impossibilita a avaliação do nível de gastos dos clientes. Sem esses dados, é difícil realizar análises financeiras detalhadas que poderiam ajudar a prever o comportamento de pagamento e o risco de inadimplência.
    
    A não especificação dos valores gastos em diferentes tipos de empréstimos e a falta de clareza sobre quais tipos de empréstimos estão incluídos na variável "outros empréstimos" dificultam uma análise detalhada. A distinção clara entre os tipos de empréstimos é crucial para entender melhor os riscos associados a cada categoria.