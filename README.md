# Mapa auto-organizável para análise de pacientes

Projeto de aprendizado não supervisionado que utiliza um mapa auto-organizável (SOM — Self-Organizing Map) para agrupar e visualizar perfis de pacientes com insuficiência cardíaca.

## Objetivo

Investigar se uma rede neural não supervisionada consegue organizar pacientes por similaridade clínica sem receber a variável de óbito durante o treinamento.

## Pipeline

1. Carregamento do Heart Failure Clinical Records Dataset.
2. Remoção de `DEATH_EVENT` do conjunto usado no treinamento.
3. Normalização das variáveis com `MinMaxScaler`.
4. Treinamento de um mapa hexagonal de 10 × 10 neurônios com `MiniSom`.
5. Avaliação com erro de quantização e erro topográfico.
6. Visualização dos agrupamentos e análise posterior dos rótulos clínicos.

O rótulo de óbito é utilizado apenas na etapa final de interpretação dos mapas; ele não participa do treinamento do SOM.

## Tecnologias

- Python
- Jupyter Notebook
- NumPy e Pandas
- MiniSom
- scikit-learn
- Matplotlib e Seaborn

## Como executar

```bash
pip install numpy pandas matplotlib seaborn scikit-learn minisom jupyter
jupyter notebook SOM.ipynb
```

## Observação

Este é um estudo acadêmico de agrupamento e visualização. Os resultados não devem ser interpretados como diagnóstico ou como um sistema clínico validado.
