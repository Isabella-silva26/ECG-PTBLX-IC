# ECG-PTB-LX

Este repositório contém os scripts, notebooks de análise e documentação do Projeto de Pesquisa focado na análise e classificação de sinais de Eletrocardiograma (ECG) utilizando o dataset **PTB-XL** (12 derivações, 100 Hz).

O objetivo principal é o desenvolvimento de uma pipeline de pré-processamento e modelagem para a classificação binária de exames entre **NORM** (Normal) e **MI** (Myocardial Infarction / Enfarto do Miocárdio).

---

## Estrutura do Repositório

* `01_eda_ptbxl.ipynb`: Notebook com a Análise Exploratória de Dados (EDA), tratamento de outliers (remoção de dados inconsistentes como `age=300`) e divisão dos dados estratificada por paciente (`patient_id`) para evitar vazamento de dados (*data leakage*).
* `Dicionário_e_Fluxo_de_Dados_PTBXL.pdf`: Documento com a descrição detalhada das variáveis do dataset e o fluxo metodológico da pipeline.
* `Artigos_ficha.pdf`: Fichamento de leitura e referências bibliográficas que embasam a metodologia do projeto.

---

## Metodologia & Pré-processamento

* **Dataset:** PTB-XL (sinais reduzidos a 100 Hz - `filename_lr`).
* **Tratamento de Dados:** Filtro das classes NORM vs. MI e limpeza de registros inconsistentes.
* **Normalização:** Z-score aplicada aos sinais.
* **Divisão de Dados:** Separação entre treino, validação e teste realizada estritamente por paciente para garantir a integridade da avaliação do modelo.

---

## Próximos Passos

1. Implementação do gerador de lotes (*batch generator*) para carregamento dos sinais na memória.
2. Construção e treinamento da arquitetura de Deep Learning (CNN 1D) para classificação.
3. Avaliação de métricas de desempenho (Acurácia, F1-Score, AUC-ROC).
