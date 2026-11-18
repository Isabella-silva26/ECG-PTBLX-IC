# ECG-PTB-XL

Este repositório contém os notebooks de análise e a documentação do Projeto de Pesquisa focado na classificação de sinais de eletrocardiograma (ECG) com o conjunto de dados público PTB-XL (12 derivações, versão de 100 Hz).

O objetivo principal é desenvolver um pipeline reprodutível de pré-processamento e modelagem para a classificação binária de exames entre NORM (normal) e MI (infarto do miocárdio), com separação dos dados por paciente para evitar vazamento de dados (*data leakage*).

**Status atual:** etapa exploratória concluída (análise dos metadados, rotulagem, divisão por paciente, leitura e normalização de um ECG de exemplo). Nenhum classificador foi treinado ainda.

## Estrutura do repositório

- `setembro/01_eda_ptbxl.ipynb`: notebook da análise exploratória (EDA). Inclui a rotulagem NORM vs MI pela tabela oficial `scp_statements.csv`, o tratamento da idade anonimizada, a análise por paciente, a divisão treino/validação/teste por `patient_id` (sem vazamento), o carregamento e a normalização de um ECG de exemplo e testes mínimos.
- `setembro/Dicionário_e_Fluxo_de_Dados_PTBXL.pdf`: dicionário das variáveis e fluxo de dados do pipeline.
- `setembro/Artigos_ficha.pdf`: fichas de leitura dos artigos que embasam a metodologia.

## Como executar

1. Abra `setembro/01_eda_ptbxl.ipynb` no Google Colab.
2. Execute todas as células em ordem (Ambiente de execução → Executar tudo). É necessário ter internet: os metadados e o ECG de exemplo são baixados diretamente do PhysioNet, e os dados não ficam armazenados neste repositório.
3. Para executar fora do Colab, instale as dependências com `pip install wfdb pandas numpy matplotlib seaborn scikit-learn` e remova a primeira célula (montagem do Google Drive).

## Dados

- PTB-XL v1.0.3 (PhysioNet): https://physionet.org/content/ptb-xl/1.0.3/ — 21.799 exames de 10 s com 12 derivações.
- Referência: Wagner, P. et al. (2020). PTB-XL, a large publicly available electrocardiography dataset. *Scientific Data*, 7, 154.

## Metodologia e pré-processamento (etapa atual)

- **Sinais:** versão de 100 Hz fornecida pelo dataset (`filename_lr`), com 12 derivações.
- **Rotulagem:** cada código SCP é convertido em superclasse diagnóstica pelo `scp_statements.csv`. Exames apenas com NORM recebem a classe NORM. Exames apenas com MI (incluindo IMI, ASMI, ALMI e os códigos de lesão subendocárdica INJAS, INJAL, INJLA, INJIN e INJIL) recebem a classe MI. Os demais, inclusive os que têm NORM e MI ao mesmo tempo, recebem "Outros" e ficam fora da tarefa.
- **Idade:** no PTB-XL, idades acima de 89 anos aparecem como 300 (anonimização). Esses exames são mantidos, com a idade limitada a 90 anos e marcação na coluna `idade_anonimizada` (a idade original fica em `age_original`).
- **Normalização:** Z-score por derivação, demonstrado em um ECG de exemplo (a aplicação em lote a todos os exames ainda está pendente).
- **Divisão dos dados:** por `patient_id`, em aproximadamente 70% treino, 15% validação e 15% teste (`random_state=42`). O notebook verifica que nenhum paciente aparece em mais de um conjunto. A divisão ainda não é estratificada por classe.

## Resumo da etapa exploratória

- Tarefa NORM vs MI: 14.981 exames (9.513 NORM e 5.468 MI) de 13.435 pacientes; 129 pacientes têm exames das duas classes.
- Idade anonimizada (acima de 89 anos): 152 exames.
- Treino: 10.513 exames e 9.404 pacientes. Validação: 2.258 exames e 2.015 pacientes. Teste: 2.210 exames e 2.016 pacientes.

## Próximos passos

1. Pré-processamento reprodutível (normalização e, se justificado, filtragem passa-banda), aplicado em lote a todos os sinais.
2. Baselines clássicos (regressão logística, Random Forest e SVM ou gradient boosting), com script de treino e avaliação separado do notebook e configuração com seed.
3. Comparação entre divisão aleatória por registro e divisão por paciente (com estratificação das classes).
4. Avaliação com métricas adequadas ao desbalanceamento: sensibilidade, especificidade, F1, MCC, AUROC, AUPRC e matriz de confusão.
5. Validação em uma segunda base compatível (PTB Diagnostic ECG), se as bases forem comparáveis.
6. Opcional: CNN 1D com o mesmo protocolo; calibração e relatório final.
