# Analise_Preditiva_de_Urbanizacao-_Ecorregiao_Bahia_Coastal_Forests
Este projeto desenvolve modelos de Machine Learning para identificar áreas urbanizadas na ecorregião das florestas costeiras da Bahia, utilizando dados ambientais e climáticos. O desafio principal reside no forte desbalanceamento das classes e na necessidade de otimizar métricas específicas de sensibilidade.

## 📌 Contexto do Problema
O objetivo é classificar se uma amostra geográfica (área de ~0,737 km²) é **urbanizada** ou **não urbanizada**. Para isso, utilizamos dados de sensoriamento remoto (NDVI, cobertura arbórea) e 19 variáveis bioclimáticas do WorldClim.

### Especificações dos Dados
* **Dataset**: 16.502 amostras da ecorregião *Bahia Coastal Forests*.
* **Variável Alvo**: Criada a partir da coluna `SMOD` (Geralmente usada para grau de urbanização).
  * **Urbanizado (1)**: `SMOD >= 20`.
  * **Não Urbanizado (0)**: `SMOD < 20`.

## 🛠️ Metodologia e Solução

### 1. Engenharia de Atributos e Pré-processamento
* **Prevenção de Data Leakage**: A variável `SMOD` foi removida do conjunto de treinamento. Como o alvo foi derivado dela, mantê-la causaria um vazamento de dados, invalidando a capacidade de generalização do modelo.
* **Tratamento de Categóricas**: A coluna `koppen_geiger` (classificação climática) foi transformada via *One-Hot Encoding* para evitar que o modelo interpretasse os códigos numéricos como uma ordem de grandeza.
* **Escalonamento**: Aplicado o `StandardScaler` para normalizar as variáveis contínuas, garantindo estabilidade no treinamento da Rede Neural.

### 2. Definição de Métricas Críticas
O projeto exige o cumprimento de duas metas de desempenho fundamentais:
* **Métrica 1 (Recall/Sensibilidade)**: Identificada como a mais apropriada para garantir que o máximo de áreas urbanas reais sejam detectadas (Meta > 70%).
* **Métrica 2 (Precisão)**: Utilizada para assegurar que, das áreas previstas como urbanas, ao menos 70% sejam de fato urbanizadas.

### 3. Modelagem Comparativa
Foram implementados três modelos com complexidades distintas:
1. **Modelo 1 (Baseline)**: Regressão Logística.
2. <img width="575" height="466" alt="image" src="https://github.com/user-attachments/assets/1dfb75b0-471f-4829-8cfb-0bb394c9d0ab" />

3. **Modelo 2 (Avançado)**: Random Forest Classifier, utilizando o parâmetro `class_weight='balanced'` para compensar o desbalanceamento (apenas 6,5% dos dados são da classe positiva).
4. <img width="943" height="466" alt="image" src="https://github.com/user-attachments/assets/90d98a86-4a71-4ab4-8fb9-f811449ee4b1" />

5. **Modelo 3 (Deep Learning)**: Rede Neural (MLP) com 2 camadas ocultas. Para evitar *overfitting*, foram utilizados **Dropout** e **Early Stopping**.
6. <img width="625" height="361" alt="image" src="https://github.com/user-attachments/assets/c8f9e647-c2c8-4e6c-9d2e-b6327b9c2e3c" />


## 📈 Conclusões e Resultados

### Importância das Variáveis (Feature Importance)
Através do Random Forest, identificou-se que o **NDVI** (Índice de Vegetação) e o **TreeCover** (Cobertura Arbórea) são os preditores mais impactantes. Isso confirma que a substituição da cobertura vegetal por estruturas urbanas é o padrão geográfico mais forte detectado pelo modelo.
<img width="1054" height="567" alt="image" src="https://github.com/user-attachments/assets/f765b962-a697-43f0-bf6e-0ea7cbae2981" />


### Otimização via Ajuste de Threshold
O modelo padrão (threshold 0.5) apresentou um Recall abaixo do esperado. A solução adotada foi a **alteração do limiar de probabilidade** .
<img width="756" height="383" alt="image" src="https://github.com/user-attachments/assets/6f421d9d-5223-45bd-8fe3-248f6023e5d4" />
<img width="668" height="654" alt="image" src="https://github.com/user-attachments/assets/fa0c5f0e-3885-41cc-a4d7-f8bb28eff442" />



| Cenário | Threshold | Recall (Sensibilidade) | Precisão |
| :--- | :---: | :---: | :---: |
| Conservador | 0.50 | 47.3% | Alta |
| **Equilibrado (Recomendado)** | **0.30** | **72.9%** | **73.3%** |
| Alta Sensibilidade | 0.20 | 80.6% | 62.2% |

**Justificativa**: Ao reduzir o limiar para **0.30**, o modelo atingiu simultaneamente ambas as metas do projeto (> 70% de Recall e Precisão), oferecendo uma ferramenta robusta para o monitoramento ambiental e urbano.

---
*Este projeto compõe um Case Técnico de Ciência de Dados (Dez/2025).*
