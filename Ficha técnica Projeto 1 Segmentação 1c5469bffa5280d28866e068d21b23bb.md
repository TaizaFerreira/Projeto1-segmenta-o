# Ficha técnica Projeto 1: Segmentação

Criada em: 28 de março de 2025 23:43
: Tecnologia

# **Objetivo**

Objetivo do projeto é analisar e preparar banco de dados da empresa O Mercado para identificar o perfil de clientes e gerar insights estratégicos.

# **5.1 Processar e preparar banco de dados**

# 5.1.1 🔵 Conectar/importar dados para ferramentas

Realizado uma cópia das 3 tabelas clientes, transacoes e resumo_compras da base de dados fornecida pela laboratória e enmeada cada uma com seu nome. 

Rcriado nova planilha com abas específicas para cada uma das cópias e fiz a importação utilizando o **IMPORTRANGE** das tabelas **clientes, transacoes e resumo_compras, cumprindo a meta estabelecida.**

**(=IMPORTRAGE(“URL da página da planilha”;”nome_da_tabela!selecionar toda tabela”)**

```sql
=IMPORTRANGE("https://docs.google.com/spreadsheets/d/1spKF64GtpLSfBvaPX6Pm4X7wiSD9InjvvGHwvBAMIpQ/edit?gid=0#gid=0";"clientes!A1:I2241")
=IMPORTRANGE("https://docs.google.com/spreadsheets/d/119tRPyG0Yqw_Rl_ypRwFX8nFluuVXVKzsvNPc_DPZbk/edit?gid=0#gid=0";"A1:D22128")
=IMPORTRANGE("https://docs.google.com/spreadsheets/d/1Ch-Z5yLUyucqjQDKfu0nZwtzkAaF-OK1hubyuPgsEVs/edit?gid=1803379532#gid=1803379532";"resumo_compras!A1:G2250")
```

# 5.1.2 🔵 Identificar e tratar valores nulos

Para identificar os valores nulos apliquei em cada tabela a fórmula contar.vazio.

```sql
**=CONTAR.VAZIO(A2:I2241)**
```

Foi identificado na tabela de clientes 24 valores nulos na coluna salario_anual_dolar, identificado onde estavam os nulos aplicando o filtro e localizado  os espaços em branco. Na tabela transações haviam 7 valores nulos. Na tabela resumo_compras não havia valores nulos.

# 5.1.3 🔵 Identificar e tratar valores duplicados

Para a limpeza de duplicados utilizei nas 3 tabelas o caminho **dados, limpeza de dados, remover cópias (na ferramenta google sheets).** Identificado 9 valores duplicados na tabela **resumo_compras,** nas demais planilhas não havia duplicados.

# 5.1.4 🔵 Identificar e gerenciar dados fora do escopo da análise

Foi identificado na tabela **transações** 7 valores sem identificação  do cliente, optado por remover da análise, pois sem o ID não teria como associar as demais informações na análise.

Foi identificado 10 clientes com transações 0, entendo que o cliente apenas foi cadastrado e não efetivou a transação, sendo irrelevante para a análise.

Na tabela clientes na coluna salario_anual_dolar, havia células em branco, marcado na coluna como não informado, após realizado a mediana dos salários por grau de escolaridade para fornecer a media do salário para os que não tinha esse dado fornecido, mediana realizada por escolaridade.

```sql
=MED(FILTER(clientes!E:E; clientes!C:C="posgrado"))
=MED(FILTER(clientes!E:E; clientes!C:C="grado o superior"))
=MED(FILTER(clientes!E:E; clientes!C:C="Secundaria"))
```

Também havia 1 salario com o valor 666.666 por se tratar de um valor estranho, optei também por fazer a mediana do mesmo juntamente com os nulos.

Mediana de salário para Grado o superior $52.029 , posgrado $54.110  e secundaria $36.223 .

Utilizado a fórmula abaixo para buscar para a planilha os resultados da mediana na coluna salrio_por_educacao.

```sql
=ARRAYFORMULA(SE(E2:E2241 = ""; SE(C2:C2241 = "Secundaria"; 36223; SE(C2:C2241 = "Grado o superior"; 52028; SE(C2:C2241 = "Posgrado"; 54019; ""))); SE(E2:E2241 > 100000; 52028; SE(E2:E2241 < 1000; 36223; E2:E2241))))
```

Identificado 3 anos de nascimento fora do escopo (1893,1899 e 1900), realizado a mediana com a mesma linha de pensamento do salário.

```sql
=MED(FILTER(clientes!B:B; clientes!C:C="Secundaria"))
=MED(FILTER(clientes!B:B; clientes!C:C="Grado o superior"))
=MED(FILTER(clientes!B:B; clientes!C:C="Posgrado"))

```

Podendo se tratar de erro de digitação foi optado encontrar a mediana dos anos de nascimento para os de nível secundário 1975, posgrado 1968 e grado o superior 1970.

Para trazer esses valores para a tabela utilizado a fórmula na coluna ano_por_educacao:

```sql
=ARRAYFORMULA(SE(B2:B2241 < 1940; SE (D2:D2241 = "Secundaria"; 1975; SE (D2:D2241 = "Grado o superior"; 1970; SE (D2:D2241 = "Posgrado"; 1968; " "))); B2:B2241)) 
```

# 5.1.5 🔵 Unir tabelas

Realizado a união das tabelas utilizando para tabela clientes abrindo uma nova aba e nomeando como dados_consolidados as seguinte fórmulas:

```sql
=ÍNDICE(clientes!F20;CORRESP(clientes!F20;clientes!F20:F2260;0))
```

Com a fórmula Índice foi realizada a exportação da tabela Clientes por completo, utilizando essa fórmula por ser mais flexível facilitando a junção das tabelas.

```sql
=PROCV(A2;resumo_compras!$A$1:G;2;0)
```

Com a fórmula PROCV eu busquei na tabela resumo_compras o total de cada item comprado de acordo com um valor correspondente em ambas as tabelas o Id.

```sql
=CONT.SE(transacoes!B:B; A2)
```

Com a fórmula CONT.SE foi realizado busca na tabela transações o número de transações por cliente, fiz a alteração do título da coluna de id_transação para n_transação_cliente.

```sql
=SEERRO(MÁXIMO(FILTER(transacoes!C:C;transacoes!B:B=A2));"sem transacao")
```

A fórmula SEERRO foi escolhida para análise dos dados a data da última transação dos clientes, utilizando a fórmula SEERRO, MÁXIMO (data maior), FILTER (para filtrar a data maior da transação). Alterado título da coluna de data_transacão para data_utima_transacao. Alterado para DD/MM/AA

```sql
=SE(CONT.SES(transacoes!B:B; A2; transacoes!D:D; "tienda") > CONT.SES(transacoes!B:B; A2; transacoes!D:D; "en línea"); "tienda"; SE(CONT.SES(transacoes!B:B; A2; transacoes!D:D; "en línea") > CONT.SES(transacoes!B:B; A2; transacoes!D:D; "tienda"); "en línea"; "ambos"))
```

SE(CONT.SES) foi a fórmula escolhida para puxar os dados de vendas online e na loja, no caso se as vendas forem maior online viria descrito en linea, se for maior na loja viria descrito tienda, se o número de compras nos dois canais for igual retorna ambos.

# 5.1.6 🔵 Criar novas variáveis

Realizado a criação de novas variáveis para otimizar o processo de análise, algumas delas inclusive auxilia no processo da análise RFM.

Dentre as apresentadas abaixo, as demais já foram citadas acima que seria: salario_educacao e ano_nascimento.

Criado variável mes_entrada 

```sql
=TEXTO(H2; "mmmm")
```

A variável foi criada para que possamos analisar em que mês os clientes mais deram entrada.

Dados foram retirados da coluna H (data_entrada).

Ex: análise: nos meses de novembro e dezembro a procura é maior devido às festas de fim de ano. ficar no estoque de vendas e em horários da loja.

Criado variável ano_de_entrada

```sql
=ANO(H2)
```

Foi criado a variável ano_entrada para que possamos analisar em que mês os clientes mais deram entrada.

Dados foram retirados da coluna H (data_entrada).

Criado variável idade_2

```sql
=ANO(HOJE()) - AJ2 
```

Criado variável idade, puxando o valor da coluna AJ ano do nascimento, utilizando fórmula acima para análise das idades.

```sql
=IFS( E(ANO(HOJE())-B3>=18; ANO(HOJE())-B3<=30); "Jovem Adulto - 18 a 30 anos"; E(ANO(HOJE())-B3>=31; ANO(HOJE())-B3<=50); "Adulto - 31 a 50 anos"; E(ANO(HOJE())-B3>=51; ANO(HOJE())-B3<=64); "Meia-idade - 51 a 64 anos"; VERDADEIRO; "Idoso - Maior que 65 anos" )
```

Criado variável faixa_etaria para melhor vizualização da faixa de maior compra em números e de maior compras online e loja física.

18 a 30 anos - jovem adulto, 31 a 50 anos- adulto, 51 a 64 anos meia idade, idoso -maior que 65 anos.

Criado variável dias_ultima_compra para auxiliar a aplicação da fórmula RFM

```sql
=SEERRO(DATA(2022;12;31) - R2; 0)
```

Criado nova variável total_gasto_por_cliente para auxiliar a aplicação da fórmula RFM, somando o total de compra.

```sql
=SOMA(K2:P2)
```

# **5.2 Fase análise exploratória:**

# 5.2.1 🟣 Agrupar dados de acordo com variáveis categóricas

Você criou tabelas dinâmicas para resumir informações por nível educação, estado civil, etc?

Criado tabelas dinâmicas para resumir as informações da tabela, para uma melhor visualização e análise. Pensando em organização foi elencado algumas perguntas para direcionamento, exemplo: Qual é a faixa etária mais presente?, escolaridade?, estado civil?, media salarial?, lugar de transação?, item mais vendido?

# 5.2.2 🟣 **Visualizar variáveis categóricas**

Você criou gráficos de colunas para representar dados agrupados por variáveis categóricas, como o número de pessoas que responderam à campanha de marketing realizada pela empresa?

Através da informações obtidas com a criação das tabela dinâmicas, criei gráficos  de colunas, barras, pizza para melhor visualização dos dados encontrados.

# 5.2.3 🟣 **Calcular o quartil, decil ou percentil**

Para análise optei a utilização do quartil. 

Foi necessário criar uma nova variável subtraindo a data atual da última data da compra (dias_ultima_compra).

```sql
=SEERRO(HOJE() - R2; 0)
```

Foi necessário criar nova variável de soma dos itens totais de compra, com o título total_gasto_por_cliente, valor em dólar.

```sql
=SOMA(K2:P2)
```

Para calcular o quartil foi utilizado a fórmula =QUARTIL(intervalo; número_do_quaril).

Criado uma tabela de 5 linhas dentro da aba dados consolidados com os títulos Quartil (com valores 0,1,2,3,4), recencia (com valores 4,3,2,1,0), frequencia e monetário.

Para realizar o cálculo do quartil para encontrar o quao_recente (dados da coluna V **dias_ultima_compra )** foi utilizado a seguinte fórmula:

```sql
=QUARTIL(V2:V2241;0)
```

Para realizar o cálculo do quartil para encontrar a frequencia (dados da coluna Q n_transacao_cliente), utilizado a fórmula:

```sql
=QUARTIL(Q2:Q2241;0)
```

Para realizar o cálculo do quartil para encontrar o valor monetário  (dados da coluna W **Total_gasto_por_cliente), utilizei a fórmula:**

```sql
=QUARTIL(W2:W2241;0)
```

Criado novas colunas uma para recencia, frequencia e valor monetario, para armazenar os valores que a fórmula do quartil irá trazer.

Utilizado a fórmula para encontrar o valor de cada cliente na recencia (utilizado as colunas: dias_da_ultima_compra, quartil e quao_recente)

```sql
=IFS(
  V2 <= AD$14; AC$14;
  V2 <= AD$13; AC$13;
  V2 <= AD$12; AC$12;
  V2 <= AD$11; AC$11;
  V2 <= AD$10; AC$10;
  VERDADEIRO; "Fora da faixa"
)
```

E frequência: (utilizado as colunas: transação_cliente, quartil e frequencia)

```sql
=IFS(Q2<=AD$2;AC$2;E(Q2>AD$2;Q2<=AD$3);AC$3;E(Q2>AD$3;Q2<=AD$4);AC$4;E(Q2>AD$4;Q2<=AD$5);AC$5;E(Q2>AD$5;Q2<=AD$6);AC$6)
```

E o monetário (utilizando as colunas quartil, monetário e total_gasto_por_cliente

```sql
=IFS(W2<=AE$2;AC$2;E(W2>AE$2;W2<=AE$3);AC$3;E(W2>AE$3;W2<=AE$4);AC$4;E(W2>AE$4;W2<=AE$5);AC$5;E(W2>AE$5;W2<=AE$6);AC$6)
```

# 5.3.1 🔴 **Aplicar segmentação **

Criado categorias para analisar clientes e nomeá-los como:

1 -  Campeões R 4, F 4, M 4

Compra recente, com frequência e gastam muito.

2 - Clientes Fiéis R 3, 4  F 3 ou 4  M 3,4

Estão ativos, compram frequentemente e gastam muito.

3 - Clientes de alto potencial R 2,3,4   F 2,3,4   M 2,3,4

Estão acima da média nos 3 quesitos mais não está no topo.

4 -  Clientes promissores R 3,4  F 0,1,2  M 0,1,2,3

Compraram recentemente, mais ainda não são frequentes e não gastam muito.

5 - Clientes neutros R 2  F 2,3   M 0,1,2,3,

Compras, recencia e valor médios

6 - Clientes em risco R 0,1,2   F 0,1,2,3,  M 2,3,4 

Estão começando a sumir mais já gastaram bem no passado.

7 - Clientes a desenvolver R 1,2  F 1,2,3  M 1,2

Estão ativos mas gastam e compram com pouca frequência.

8 - Clientes perdidos R 0,1 F 0,1  M 0,1

Não compram mais, nem gastaram muito, possivelmente clientes descartáveis.

9 - Clientes que não posso perder R 1  F 3,4  M 3,4

Compravam muito e com frequência, mas pararam recentemente.

Utilizado a fórmula IFS a qual avalia na ordem e retorna o resultado correspondente a primeira condição verdadeira:

```sql
=IFS(
   E(X2=4; Y2=4; Z2=4); "Campeões";
   E(OU(X2=3; X2=4); OU(X2=3; X2=4); OU(Y2=3; Y2=4); OU(Z2=3; Z2=4)); "Clientes Fiéis";
   E(OU(X2=2; X2=3; X2=4); OU(Y2=2; Y2=3; Y2=4); OU(Z2=2; Z2=3; Z2=4)); "Clientes de Alto Potencial";                                                 E(OU(X2=3; X2=4); OU(Y2=1; Y2=0; Y2=2); OU(Z2=0; Z2=1; Z2=2; Z2=3)); "Clientes Promissores";
   E(OU(X2=0; X2=1); OU(X2=0; X2=2); OU(Y2=0; Y2=1; Y2=2; Y2=3; Y2=4); OU(Z2=2; Z2=3; Z2=4)); "Clientes em Risco"; E(OU(X2=1); OU(Y2=2; Y2=3; Y2=4); OU(Z2=3; Z2=4)); "Clientes Que Não Posso Perder";
   E(OU(X2=0; X2=1); OU(X2=0; X2=1); OU(Y2=0; Y2=1); OU(Z2=0; Z2=1;)); "Clientes Perdidos"; E(OU(X2=2;); OU(Y2=2; Y2=3); OU(Z2=2; Z2=3)); "Clientes Neutros"; E(OU(X2=1; X2=2); OU(Y2=1; Y2=2; Y2=3); OU(Z2=1; Z2=2)); "Clientes A Desenvolver"; VERDADEIRO; "Outros"
)
```

# 5.4.1 🟠 **Represente os dados por meio de tabela resumo ou scorecards**

Realizado a construção de um dashboard com os valores totais, com a utilização de tabelas dinâmicas e fórmulas.

A página de rosto do dashboard foi criado no google apresentações, salvado a foto e importado imagem para a planilha do google sheets, alimentado s valores e gráficos na figura.

# 5.4.2 🟠 **Representar dados a través de gráficos simples**

Criado tabelas dinâmicas e gráficos de pizza, colunas, linhas, para visualizar as segmentações cientes e dos dados para melhor análise.

# **🟩 5.5 Apresentar resultados**

5.5.1 🟢 **Selecionar gráficos e informações relevantes**

Selecionado gráficos e informações que constaram na minha apresentação.

# 5.5.2 🟢 **Criar uma apresentação**

Realizado a criação de uma apresentação no google apresentações de 5 slides, constando as informações que julguei ser o caminho a seguir pela empresa  mercado.

# 5.5.3 🟢 **Apresentar resultados com conclusões e recomendações**

Realizado a gravação de um vídeo de 5 minutos, utilizando a ferramenta Loom com a apresentação dos resultados da minha análise, com conclusão, sugestões e ações.

# Links para visualização do trabalho no geral

LINKS PLANILHA DE DADOS CONSOLIDADOS

[https://docs.google.com/spreadsheets/d/1_Q_8kOmydSq2LvbKOxmgqwOCqmcR7VB2NwGvqk7w_lU/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1_Q_8kOmydSq2LvbKOxmgqwOCqmcR7VB2NwGvqk7w_lU/edit?usp=sharing)

LINK DASHBOARD

[https://docs.google.com/spreadsheets/d/1_Q_8kOmydSq2LvbKOxmgqwOCqmcR7VB2NwGvqk7w_lU/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1_Q_8kOmydSq2LvbKOxmgqwOCqmcR7VB2NwGvqk7w_lU/edit?usp=sharing)

LINK APRESENTAÇÃO

[https://www.loom.com/share/79ffe3624d3743b0806688ad00bea8b2?sid=827aedb7-af56-4446-a1b5-08ab85937280](https://www.loom.com/share/79ffe3624d3743b0806688ad00bea8b2?sid=827aedb7-af56-4446-a1b5-08ab85937280)