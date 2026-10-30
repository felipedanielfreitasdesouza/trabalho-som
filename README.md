# Análise de perfis clínicos com mapa auto-organizável (SOM)

Projeto de aprendizado **não supervisionado** que usa um mapa auto-organizável — **SOM, Self-Organizing Map** — para organizar pacientes com insuficiência cardíaca por similaridade clínica. O mapa cria uma representação bidimensional dos dados e permite investigar se perfis semelhantes acabam próximos uns dos outros.

A coluna `DEATH_EVENT` é mantida fora do treinamento. Ela só é usada depois, para colorir os mapas e verificar se os agrupamentos encontrados possuem alguma coerência com o desfecho observado.

## Dataset e variáveis

O projeto usa o `heart_failure_clinical_records_dataset.csv`, com **299 registros**. São utilizadas 12 variáveis clínicas:

```text
age, anaemia, creatinine_phosphokinase, diabetes,
ejection_fraction, high_blood_pressure, platelets,
serum_creatinine, serum_sodium, sex, smoking, time
```

O alvo `DEATH_EVENT` é removido de `X` antes do treinamento e guardado separadamente em `y` para a análise posterior.

## Por que um SOM?

Diferentemente de uma MLP supervisionada, o SOM não recebe a resposta correta durante a aprendizagem. Ele tenta preservar relações de vizinhança: pacientes com vetores clínicos parecidos tendem a ser associados a neurônios próximos no mapa.

Essa abordagem é útil para:

- explorar a estrutura interna do dataset;
- encontrar regiões de maior densidade de pacientes;
- visualizar como os atributos mudam pela malha;
- observar, retrospectivamente, onde os casos com `DEATH_EVENT=1` se concentram.

## Pré-processamento

1. O CSV é carregado com Pandas.
2. `DEATH_EVENT` é separado e não entra no modelo.
3. Todas as 12 entradas são transformadas para `[0, 1]` com `MinMaxScaler`.
4. A escala comum é importante porque o SOM usa distância entre vetores; sem ela, variáveis como `platelets` dominariam variáveis com valores menores.

## Configuração do mapa

| Parâmetro | Valor | Motivo |
|---|:---:|---|
| Tamanho | 10 × 10 | 100 neurônios para representar 299 pacientes. |
| Topologia | Hexagonal | Oferece uma vizinhança mais rica entre neurônios. |
| `sigma` | 1.5 | Raio inicial de influência da vizinhança. |
| Learning rate | 0.5 | Velocidade de atualização dos pesos. |
| Iterações | 10.000 | Tempo suficiente para estabilização no experimento. |
| Inicialização | PCA | Acelera a organização inicial dos pesos. |
| Semente | 42 | Torna a configuração do notebook repetível. |

O treinamento é feito com `MiniSom.train_random`.

## Visualizações geradas

- **Hit Map:** mostra em quais neurônios os pacientes caíram e quantos casos cada região recebeu.
- **Classes dominantes:** colore cada neurônio ocupado pela classe majoritária de `DEATH_EVENT`, sem usar esse rótulo para treinar.
- **Component Planes:** exibe os valores dos pesos da malha para `age`, `ejection_fraction`, `serum_creatinine` e `time`.
- **Projeções dos pesos:** compara os neurônios e os pacientes em pares de atributos selecionados.

## Métricas do snapshot executado

O notebook registra:

| Métrica | Valor | Interpretação |
|---|---:|---|
| Erro de quantização | **0,3515** | Distância média entre cada amostra e seu neurônio vencedor. |
| Erro topológico | **0,0201** | Frequência com que a vizinhança dos dados não é preservada corretamente. |

Essas métricas avaliam a qualidade do mapa, não são acurácia de classificação. Uma baixa taxa de erro topológico sugere boa preservação da estrutura de vizinhança, mas não prova capacidade de diagnóstico.

## Como executar

Na raiz do repositório:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install numpy pandas matplotlib seaborn scikit-learn minisom jupyter
jupyter notebook SOM.ipynb
```

Execute as células em ordem. O CSV já está na pasta do projeto.

## Estrutura

```text
Trabalho de SOM/
├── SOM.ipynb
├── heart_failure_clinical_records_dataset.csv
└── README.md
```

## Limitações

- O notebook não separa treino e teste; trata-se de uma exploração descritiva do dataset inteiro.
- O rótulo de óbito é usado na visualização posterior, portanto as cores dos mapas não devem ser confundidas com uma avaliação supervisionada.
- O tamanho 10 × 10 e os demais hiperparâmetros são escolhas experimentais, não uma configuração clinicamente validada.
- A associação visual entre regiões do mapa e desfechos não implica causalidade.
- Este projeto não é um sistema de diagnóstico nem uma ferramenta de apoio médico pronta para uso.
