# wag-all-around-prediction-rotterdam-2026
Predição do resultado do individual geral feminino no campeonato mundial de ginástica artística 2026.
# WAG All-Around Prediction: Rotterdam 2026

## Introdução

A ginástica artística feminina, especialmente na modalidade de individual geral (All-Around, AA), representa um dos contextos mais complexos de avaliação de desempenho esportivo, pois exige a integração de múltiplas dimensões técnicas em quatro aparelhos distintos: salto (VT), barras assimétricas (UB), trave (BB) e solo (FX). A natureza multidimensional do desempenho, combinada com a variabilidade inerente à execução, torna a previsão de resultados uma tarefa desafiadora, tanto do ponto de vista estatístico quanto esportivo.

Nos últimos anos, abordagens baseadas em ciência de dados têm sido amplamente utilizadas para análise de performance e previsão de resultados em esportes (Bunker & Thabtah, 2019; Baio & Blangiardo, 2010). No entanto, há uma lacuna significativa na aplicação dessas técnicas à ginástica artística, especialmente considerando a estrutura de dados disponível, que frequentemente inclui competições com formatos distintos (individual geral vs. finais por aparelho).

Diante disso, este projeto propõe um modelo exploratório de predição para o individual geral feminino na competição de Rotterdam 2026, utilizando dados observados entre 2025 e 2026. A abordagem integra informações provenientes de competições com individual geral e competições por aparelho, buscando capturar simultaneamente desempenho global, forma recente e estabilidade competitiva.

---

## Materiais

A base de dados foi construída manualmente a partir de resultados oficiais e públicos de competições internacionais de ginástica artística feminina (WAG), contemplando o período de 2025 a 2026.

Foram consideradas duas categorias de competições:

### Competições com Individual Geral (AA)

Estas competições fornecem diretamente a variável de interesse (nota final no individual geral):

- Campeonato Mundial de Ginástica Artística 2025  
- Campeonato Pan-Americano 2025  
- Campeonato Asiático 2025  
- City of Jesolo Trophy 2026  

### Competições por Aparelho (World Cups / Challenge Cups)

Estas competições foram utilizadas como proxy de forma recente e desempenho técnico:

- Cottbus World Cup (2025, 2026)  
- Baku World Cup (2025, 2026)  
- Antalya World Cup 2025  
- Cairo World Cup 2026  
- Osijek World Cup (2025, 2026)  
- Doha World Cup 2025  
- Szombathely Challenge Cup 2025  

### Variáveis coletadas

Para cada ginasta, foram registradas:

- Nome  
- Nacionalidade  
- Evento  
- Ano  
- Notas por aparelho (VT, UB, BB, FX)  
- Nota de individual geral (AA), quando disponível  

---

## Métodos

### Definição da variável de desempenho

A variável de desempenho utilizada no modelo foi definida como:

$$
y_{ij} =
\begin{cases}
AA_{ij}, & \text{se disponível} \\
\sum_{k \in \{VT, UB, BB, FX\}} s_{ijk}, & \text{se todos os aparelhos disponíveis}
\end{cases}
$$

onde:

- $y_{ij}$ representa a nota da ginasta $i$ no evento $j$  
- $AA_{ij}$ é a nota de individual geral  
- $s_{ijk}$ representa a nota no aparelho $k$  

Essa construção permite integrar competições heterogêneas, preservando a informação mais relevante quando disponível.

---

### Sistema de ponderação

Cada observação recebeu um peso definido como:

$$
w_{ij} = w_{\text{evento}} \cdot w_{\text{recência}} \cdot w_{\text{tipo}}
$$

onde:

- $w_{\text{evento}}$ depende da relevância da competição:
  - Mundial = 1.00  
  - Continental = 0.75  
  - Internacional = 0.60  
  - Copa do Mundo = 0.45  

- $w_{\text{recência}}$:
  - 2026 = 1.20  
  - 2025 = 1.00  

- $w_{\text{tipo}}$:
  - AA real = 1.00  
  - Soma de aparelhos = 0.70  

---

### Engenharia de atributos

Para cada ginasta $i$, foram construídas as seguintes variáveis:

- Média ponderada:

$$
\mu_i = \frac{\sum_j w_{ij} y_{ij}}{\sum_j w_{ij}}
$$

- Melhor desempenho:

$$
M_i = \max_j y_{ij}
$$

- Última observação:

$$
L_i = y_{i, t_{\max}}
$$

- Desvio padrão (instabilidade):

$$
\sigma_i = \sqrt{\frac{1}{n_i} \sum_j (y_{ij} - \mu_i)^2}
$$

- Forma por aparelho:

$$
A_i = \frac{1}{K} \sum_{k=1}^{K} s_{ik}
$$

onde $K$ é o número de observações por aparelho.

---

### Definição do score preditivo

O score projetado foi definido como:

$$
S_i =
0.65 \cdot \mu_i +
0.20 \cdot M_i +
0.10 \cdot L_i +
\beta_1 A_i +
\beta_2 n^{AA}_i -
\gamma_1 \sigma_i -
\gamma_2 \frac{1}{\sqrt{n_i}}
$$

onde:

- $n^{AA}_i$ é o número de participações em AA real  
- $n_i$ é o número total de observações  
- $\beta_1, \beta_2, \gamma_1, \gamma_2$ são parâmetros empíricos  

Essa formulação é inspirada em modelos de ranking esportivo e avaliação de performance (Glickman, 1999; Hvattum & Arntzen, 2010).

---

### Conversão para probabilidade

Os scores foram transformados em probabilidades via função softmax:

$$
P(i) = \frac{\exp\left(\frac{S_i}{T}\right)}{\sum_j \exp\left(\frac{S_j}{T}\right)}
$$

onde $T$ é o parâmetro de temperatura.

A função softmax permite interpretar os scores como probabilidades relativas, garantindo normalização e comparabilidade (Bishop, 2006).

---

### Interpretação do modelo

O modelo combina três dimensões fundamentais:

1. **Desempenho histórico (AA)**  
2. **Forma recente (aparelhos)**  
3. **Estabilidade competitiva (variância)**  

Essa abordagem permite capturar tanto o nível médio quanto o potencial máximo e o risco associado ao desempenho de cada ginasta.

---

## Limitações

- Base construída manualmente  
- Ausência de variáveis como D-score e execução detalhada  
- Não modela eventos aleatórios (quedas, erros)  
- Cobertura parcial de competições  

---

## Conclusão

O modelo proposto fornece uma estrutura interpretável e baseada em dados para estimar probabilidades de vitória no individual geral feminino. Ao integrar múltiplas fontes de informação e considerar aspectos como recência e estabilidade, a abordagem contribui para a análise quantitativa de desempenho na ginástica artística.

---

## Referências

- Baio, G., & Blangiardo, M. (2010). Bayesian hierarchical model for the prediction of football results. *Journal of Applied Statistics*.

- Bishop, C. M. (2006). *Pattern Recognition and Machine Learning*. Springer.

- Bunker, R. P., & Thabtah, F. (2019). A machine learning framework for sport result prediction. *Applied Computing and Informatics*.

- Glickman, M. E. (1999). Parameter estimation in large dynamic paired comparison experiments. *Journal of the Royal Statistical Society*.

- Hvattum, L. M., & Arntzen, H. (2010). Using ELO ratings for match result prediction in association football. *International Journal of Forecasting*.
