# Estudo de Caso: Análise de Transações PIX para Detecção de Fraudes

## Introdução

Neste estudo, apresento um case prático de análise de transações PIX com o objetivo de detectar fraudes. Este case foi desenvolvido como projeto final de um curso sobre processamento de dados, permitindo a aplicação do conhecimento adquirido desde a coleta e entendimento dos dados até a modelagem de um algoritmo de detecção de fraudes.

A metodologia utilizada é o CRISP-DM, que envolve as etapas de entendimento do negócio, entendimento dos dados, preparação dos dados, modelagem, avaliação e deploy (implantação). Cada uma dessas etapas será detalhada neste estudo.

O principal objetivo é limpar, pré-processar e analisar os dados de transações PIX, treinar e avaliar modelos de detecção de fraudes utilizando Spark MLlib, e ao final fornecer insights e um relatório com os principais achados para o time de negócios.

## Metodologia CRISP-DM

O CRISP-DM (Cross Industry Standard Process for Data Mining) é uma metodologia amplamente utilizada em projetos de ciência de dados e inteligência artificial. Ela fornece uma estrutura para conduzir projetos de mineração de dados desde a compreensão inicial do negócio até a implantação de modelos.

As fases do CRISP-DM são:

1. **Entendimento do Negócio:** Compreender os objetivos e requisitos do negócio, transformando isso em uma definição de problema de mineração de dados e em um plano preliminar.
  
2. **Entendimento dos Dados:** Coletar, descrever, explorar e verificar a qualidade dos dados.

3. **Preparação dos Dados:** Selecionar, limpar, construir e formatar os dados para alimentar as ferramentas de modelagem.

4. **Modelagem:** Aplicar técnicas de modelagem e algoritmos para gerar insights e prever valores de interesse.

5. **Avaliação:** Interpretar, avaliar e afinar os modelos para garantir que atendem aos objetivos de negócio.

6. **Deploy:** Produzir o modelo final e os relatórios para utilização pelos usuários finais.

A seguir, cada uma dessas etapas será detalhada em relação ao case proposto de análise de transações PIX.

## Entendimento do Negócio

O conjunto de dados utilizado contém informações de transações PIX obtidas por um grande banco brasileiro. O PIX é um meio de pagamento instantâneo desenvolvido pelo Banco Central, permitindo transferências e pagamentos entre contas bancárias em poucos segundos.

Os objetivos do banco incluem:

- Limpar, pré-processar e analisar os dados das transações.
- Entender os padrões de uso do PIX pelos clientes.
- Treinar modelos de detecção de fraudes utilizando Spark MLlib.
- Avaliar o desempenho dos modelos.
- Prover insights sobre clientes, transações e fraudes para aprimorar os processos do banco.

As principais métricas de sucesso são: precisão e recall na detecção de fraudes, insights acionáveis sobre os dados e processos, e um relatório final com uma solução customizada para a realidade e necessidades do banco.

## Entendimento dos Dados

O conjunto de dados contém informações de 100 mil transações PIX realizadas em um período de 6 meses. Cada transação é representada por um registro no formato JSON com os seguintes atributos:

- **value:** Valor da transação.
- **time:** Data e hora da transação.
- **origin:** Informações do remetente (nome, CPF/CNPJ, banco).
- **destination:** Informações do destinatário (nome, CPF/CNPJ, banco, agência, conta).
- **fraud:** Indica se a transação foi considerada fraudulenta (Sim ou Não).

O objetivo é extrair insights dessas informações para entender melhor o comportamento dos clientes e detectar transações suspeitas.

### Observações sobre os Dados:

- É necessário transformar os dados do formato JSON para o formato colunar.
- Realizar análise exploratória para entender a distribuição de valores, bancos mais utilizados, tipos de pessoas (CPF x CNPJ), etc.
- Executar engenharia de recursos para criar novas features úteis para a detecção de fraude.
- Os dados simplificados têm cerca de 150 atributos em bases reais de PIX.

## Preparação dos Dados

Nesta etapa, o objetivo é deixar os dados prontos para análise e treinamento dos modelos de machine learning. As tarefas realizadas foram:

- **Transformação do Formato:** Os dados foram originalmente fornecidos em formato JSON, convertendo-os para o formato colunar (DataFrames do Spark) para facilitar as análises.
  
- **Limpeza dos Dados:** Foram removidos ou corrigidos valores ausentes, dados duplicados e dados fora do escopo necessário para a análise.
  
- **Feature Engineering:** Novas features ou atributos foram criados para agregar mais informações para apoiar a modelagem, como hora do dia, dia da semana e mês.
  
- **Filtragem e Amostragem:** Filtrados apenas os dados necessários, dependendo do tipo de análise. Para alguns experimentos, foi utilizada amostragem dos dados.

As principais ferramentas utilizadas foram Spark SQL, DataFrames e MLlib para realizar as transformações e preparações de maneira distribuída e escalável. Também foram utilizadas bibliotecas como Pandas para análises exploratórias.

## Análise Exploratória

Após a preparação dos dados, foram realizadas uma série de análises exploratórias para entender melhor as transações PIX e identificar padrões e insights. Algumas das principais análises realizadas foram:

- **Distribuição de Valores:** A maioria das transações é de baixo valor (entre R$50 e R$500), mas existem outliers com valores muito altos.
  
- **Transações por Hora/Dia:** Há um pico de transações durante a tarde, indicando que muitas pessoas utilizam o PIX para transferências e pagamentos durante o horário comercial.
  
- **Bancos Mais Utilizados:** O banco Bradesco é o que recebe e envia o maior volume de transações PIX, seguido pelo Itaú e Nubank.
  
- **Tipos de Pessoas (CPF x CNPJ):** A maioria das transações PIX são enviadas de pessoas físicas (CPF) para pessoas físicas, enquanto transações envolvendo empresas (CNPJ) representam uma pequena fração.
  
- **Fraudes:** Apenas 2% das transações são marcadas como fraude, concentradas em poucos destinatários recebendo vários depósitos de diferentes origens.

Esses insights são relevantes, e outras análises podem ser conduzidas para explorar ainda mais os dados. A próxima etapa é utilizar essas informações para treinar modelos de detecção de fraudes.

## Modelagem e Avaliação

Para modelar o problema de detecção de fraudes, utilizei o algoritmo Random Forest. Este algoritmo constrói um modelo baseado em árvores de decisão, treinando cada árvore com uma amostra aleatória dos dados. Isso gera diversidade e evita overfitting.

O Random Forest foi escolhido por ser apropriado para problemas com muitas features (colunas dos dados) potencialmente relevantes. Ele lida bem com dados numéricos e categóricos, incorpora automaticamente feature engineering e seleção de features, e permite estimar a importância de cada feature.

### O Pipeline de Modelagem Envolveu:

- **Separação dos Dados:** Dados foram divididos em treino e teste (80% x 20%).
- **Treinamento do Modelo:** Utilizei 5-fold cross-validation.
- **Otimização de Hiperparâmetros:** Realizada com validação cruzada.
- **Avaliação do Modelo:** Testado em dados nunca vistos antes para estimar performance real.

As métricas utilizadas para avaliar o modelo foram precisão, recall e curva ROC. O modelo obteve uma precisão de 98% e recall de 94% na detecção de fraudes na base de teste. A curva ROC ficou em 92%, indicando um modelo de boa qualidade.

As features mais importantes para detecção de fraudes foram: número de transações por destinatário, hora do dia, se é final de semana, e origem de uma conta nova.

No geral, o modelo obteve um bom desempenho, podendo ser aprimorado com a adição de mais dados e features. No entanto, já serve como uma primeira solução de detecção de fraudes em PIX para o banco.

## Deploy e Relatório Final

Para finalizar o projeto, empacotei o modelo de detecção de fraudes em um pipeline Spark para facilitar o deploy em produção. Além disso, um relatório final foi gerado com as principais análises, insights e resultados do projeto.

O relatório inclui:

- Visão geral da metodologia e etapas do projeto.
- Principais insights sobre transações PIX e clientes.
- Métricas e performance do modelo de detecção de fraudes.
- Conclusões e próximos passos.

Esse relatório serve como base para apresentar os resultados para as áreas de negócio e produto do banco, permitindo que eles entendam melhor as transações PIX, os padrões identificados e tomem decisões informadas por dados para aprimorar seus processos e jornada do cliente.

O modelo desenvolvido também está pronto para ser integrado aos sistemas do banco em produção, podendo analisar novas transações PIX em tempo real e alertar sobre possíveis riscos. Isso aumenta a segurança e pode evitar fraudes que afetam os clientes.

Em resumo, este projeto aplicou uma solução analítica utilizando dados massivos do Spark, entregando um modelo customizado de detecção de fraudes em PIX e insights acionáveis para os tomadores de decisão.

## Conclusão

Neste estudo, foram apresentadas todas as etapas de um projeto analítico seguindo a metodologia CRISP-DM, desde o entendimento inicial do problema de negócio até a implantação de um modelo de machine learning em produção.

A partir da análise de transações PIX, foi possível identificar insights sobre os clientes e seus padrões de uso deste meio de pagamento, treinar um modelo de detecção de fraudes com boa performance, e compilar um relatório com as principais conclusões e recomendações.

Os conhecimentos necessários para conduzir um projeto como esse envolvem: processamento massivo de dados com Spark, análise exploratória, feature engineering, machine learning e técnicas como Random Forest. Além disso, a metodologia CRISP-DM fornece uma estrutura sólida para nortear todas as etapas.

Este case prático serve como um exemplo completo de como maximizar o valor dos dados dentro de uma organização, gerando insights estratégicos para os negócios e desenvolvendo soluções analíticas escaláveis em Big Data. Os métodos e aprendizados apresentados aqui podem ser aplicados para resolver outros problemas de negócio, em bancos ou qualquer setor que lide com grande volume e velocidade de dados.

## Tecnologias Utilizadas

- **PySpark:** Para processamento de dados em larga escala.
- **Spark MLlib:** Para modelagem e aprendizado de máquina.
- **Pandas:** Para análise exploratória de dados.
- **Jupyter Notebook:** Para desenvolvimento e apresentação dos resultados.
