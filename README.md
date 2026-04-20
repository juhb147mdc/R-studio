# R-studio
projeto em Rstudio

Aqui está uma proposta de `README.md` estruturada, profissional e alinhada com as melhores práticas de Ciência de Dados e Negócios. Ela documenta perfeitamente a complexidade do seu código e facilita o entendimento para outros analistas ou para a diretoria.

***

# 🚀 Automação de Regressão Logística: Propensão a Investimentos

Este projeto contém um pipeline automatizado em **R** projetado para modelar, prever e gerar *scores* de propensão a investimentos de cooperados. A ferramenta foi desenvolvida para apoiar a estratégia de captação de recursos, processando bases de dados agência por agência (PAs) e substituindo suposições comerciais por rigor estatístico.

O modelo treina uma Regressão Logística Binária para cada agência, isolando os fatores que levam um cooperado a investir (`flag_investidor`) e gerando uma lista priorizada de *leads* (Scoring) para a equipe comercial.

---

## 🎯 Principais Funcionalidades e Otimizações

O algoritmo vai muito além de um simples ajuste de modelo, incorporando etapas de *Data Prep* e *Feature Engineering* avançadas:

* **Tratamento de Outliers (Winsorization):** Limita valores extremos nas variáveis financeiras aos percentis 1% e 99%, evitando que super-cooperados distorçam a regressão.
* **Tratamento de Não-Linearidade:** Aplicação de transformação logarítmica (`log1p`) em variáveis monetárias para aproximar os dados de uma distribuição normal.
* **Variáveis de Interação Estratégica:** Criação de novas variáveis baseadas em conhecimento de negócio, como *Maturidade Institucional* (Idade $\times$ Tempo de Cooperativa) e *Poder de Retenção* (Saldo Positivo $\times$ Renda).
* **Controle de Multicolinearidade (VIF):** Cálculo iterativo do Fator de Inflação da Variância (VIF), removendo variáveis altamente correlacionadas (VIF > 10) para garantir a integridade dos coeficientes.
* **Seleção de Atributos (Stepwise com P-Value):** Algoritmo automatizado que remove variáveis sem significância estatística (p-value > 0.05) passo a passo, entregando o modelo mais enxuto e assertivo possível.

---

## 🛠️ Pré-requisitos

Para rodar este script, você precisará da linguagem **R** e da IDE **RStudio** instaladas. Certifique-se de instalar as dependências matemáticas e de manipulação de dados executando o comando abaixo no console do R:

```R
install.packages(c("readxl", "writexl", "dplyr", "tidyr", "ggplot2", "car", "caret", "fastDummies", "stringr"))
```

---

## 📂 Estrutura de Diretórios e Configuração

O script busca os dados em lote a partir de um diretório específico. Antes de iniciar, configure a variável `pasta_origem` no início do script para apontar para a pasta onde estão os arquivos Excel de cada agência:

```R
pasta_origem <- "C:/Caminho/Para/Suas/Bases_por_Agencia"
```

**Formato de Entrada Esperado:**
Arquivos `.xlsx` contendo a base de clientes, dividida por agência/PA, com variáveis demográficas, financeiras e a variável alvo `flag_investidor` (1 = Investidor, 0 = Não Investidor).

---

## 🔄 Como o Pipeline Funciona (O "Loop Mágico")

Para cada arquivo Excel encontrado na pasta, o robô executa rigorosamente os seguintes passos:

1.  **Limpeza Inicial:** Remoção de contas "Kids", exclusão de colunas irrelevantes de identificação e separação por tipo de pessoa (Física x Jurídica).
2.  **Agrupamento Categórico:** Consolidação de níveis educacionais semelhantes e geração de variáveis *Dummy*.
3.  **Engenharia de Features:** Escalonamento (StandardScaler), Winsorização, Log e criação de interações.
4.  **Treino e Validação:** Separação estratificada (80% treino / 20% teste) e limpeza de correlação via VIF.
5.  **Modelagem Stepwise:** Construção da Regressão Logística mantendo apenas variáveis que de fato impactam a conversão.
6.  **Geração de Resultados:** Cálculo da acurácia, Pseudo R² e predição (probabilidade de 0% a 100%) para os clientes que **ainda não são** investidores.

---

## 📦 Arquivos de Saída (Outputs)

Ao final do processamento de cada agência, o script salva **4 arquivos** na mesma pasta de origem:

1.  📄 **`Base_Tratada_PreRegressao_[Nome_Agencia].xlsx`**: A base de dados pronta, padronizada e com outliers tratados, ideal para auditoria.
2.  📊 **`Estatisticas_Logistica_[Nome_Agencia].xlsx`**: Tabela com os coeficientes da regressão, *P-Values*, *Odds Ratios* (Chance) e Intervalos de Confiança (IC 95%).
3.  📈 **`Grafico_OddsRatio_[Nome_Agencia].png`**: Gráfico visual em barras (*viridis palette*) mostrando o peso de cada variável na decisão do cliente de investir (OR > 1 aumenta a propensão, OR < 1 diminui).
4.  🎯 **`Scoring_Logistica_[Nome_Agencia].xlsx`**: O principal entregável comercial. Uma lista ordenada decrescente apenas com os clientes *não investidores*, ranqueados pela **Probabilidade** gerada pelo modelo. 

---

## ⚠️ Avisos Importantes

* **Tempo de Execução:** Devido ao rigor matemático do cálculo de VIF e do algorítmo Stepwise, o processamento logístico é intensivo. **Não interrompa a execução do console.**
* **Classes Desbalanceadas / Agências Sem Oportunidade:** Se o script detectar uma agência onde 100% dos clientes são iguais (ou todos investem ou nenhum investe), ele pulará automaticamente a modelagem por impossibilidade matemática.
