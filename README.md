# Analise_Preditiva_de_Urbanizacao-_Ecorregiao_Bahia_Coastal_Forests
Este projeto desenvolve modelos de Machine Learning para identificar áreas urbanizadas na ecorregião das florestas costeiras da Bahia, utilizando dados ambientais e climáticos. O desafio principal reside no forte desbalanceamento das classes e na necessidade de otimizar métricas específicas de sensibilidade.

## 📌 Contexto do Problema
[cite_start]O objetivo é classificar se uma amostra geográfica (área de ~0,737 km²) é **urbanizada** ou **não urbanizada**[cite: 8]. [cite_start]Para isso, utilizamos dados de sensoriamento remoto (NDVI, cobertura arbórea) e 19 variáveis bioclimáticas do WorldClim[cite: 19, 20, 21, 22].

### Especificações dos Dados
* [cite_start]**Dataset**: 16.502 amostras da ecorregião *Bahia Coastal Forests*[cite: 12, 13].
* [cite_start]**Variável Alvo**: Criada a partir da coluna `SMOD` (Geralmente usada para grau de urbanização)[cite: 15, 50].
  * [cite_start]**Urbanizado (1)**: `SMOD >= 20`[cite: 17, 50].
  * [cite_start]**Não Urbanizado (0)**: `SMOD < 20`[cite: 17, 50].

## 🛠️ Metodologia e Solução

### 1. Engenharia de Atributos e Pré-processamento
* [cite_start]**Prevenção de Data Leakage**: A variável `SMOD` foi removida do conjunto de treinamento[cite: 52]. Como o alvo foi derivado dela, mantê-la causaria um vazamento de dados, invalidando a capacidade de generalização do modelo.
* [cite_start]**Tratamento de Categóricas**: A coluna `koppen_geiger` (classificação climática) foi transformada via *One-Hot Encoding* para evitar que o modelo interpretasse os códigos numéricos como uma ordem de grandeza[cite: 47, 48].
* **Escalonamento**: Aplicado o `StandardScaler` para normalizar as variáveis contínuas, garantindo estabilidade no treinamento da Rede Neural.

### 2. Definição de Métricas Críticas
[cite_start]O projeto exige o cumprimento de duas metas de desempenho fundamentais[cite: 58]:
* [cite_start]**Métrica 1 (Recall/Sensibilidade)**: Identificada como a mais apropriada para garantir que o máximo de áreas urbanas reais sejam detectadas (Meta > 70%)[cite: 59, 60, 61].
* [cite_start]**Métrica 2 (Precisão)**: Utilizada para assegurar que, das áreas previstas como urbanas, ao menos 70% sejam de fato urbanizadas[cite: 62, 63].

### 3. Modelagem Comparativa
[cite_start]Foram implementados três modelos com complexidades distintas[cite: 64]:
1. [cite_start]**Modelo 1 (Baseline)**: Regressão Logística[cite: 65].
2. [cite_start]**Modelo 2 (Avançado)**: Random Forest Classifier, utilizando o parâmetro `class_weight='balanced'` para compensar o desbalanceamento (apenas 6,5% dos dados são da classe positiva)[cite: 66].
3. [cite_start]**Modelo 3 (Deep Learning)**: Rede Neural (MLP) com 2 camadas ocultas[cite: 68]. [cite_start]Para evitar *overfitting*, foram utilizados **Dropout** e **Early Stopping**[cite: 69].

## 📈 Conclusões e Resultados

### Importância das Variáveis (Feature Importance)
[cite_start]Através do Random Forest, identificou-se que o **NDVI** (Índice de Vegetação) e o **TreeCover** (Cobertura Arbórea) são os preditores mais impactantes[cite: 67]. Isso confirma que a substituição da cobertura vegetal por estruturas urbanas é o padrão geográfico mais forte detectado pelo modelo.

### Otimização via Ajuste de Threshold
O modelo padrão (threshold 0.5) apresentou um Recall abaixo do esperado. [cite_start]A solução adotada foi a **alteração do limiar de probabilidade** (Opção 4 do roteiro técnico)[cite: 75].

| Cenário | Threshold | Recall (Sensibilidade) | Precisão |
| :--- | :---: | :---: | :---: |
| Conservador | 0.50 | 47.3% | Alta |
| **Equilibrado (Recomendado)** | **0.30** | **72.9%** | **73.3%** |
| Alta Sensibilidade | 0.20 | 80.6% | 62.2% |

**Justificativa**: Ao reduzir o limiar para **0.30**, o modelo atingiu simultaneamente ambas as metas do projeto (> 70% de Recall e Precisão), oferecendo uma ferramenta robusta para o monitoramento ambiental e urbano.

---
*Este projeto compõe um Case Técnico de Ciência de Dados (Dez/2025).*
