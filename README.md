# Sprint 13 — Previsão de rotatividade na rede de academias Model Fitness

Projeto do curso de Data Analytics da [TripleTen](https://tripleten.com/), por **Luiz Trajano**.

Numa academia o cliente raramente cancela de forma explícita: ele vem algumas vezes, vai cada vez
menos e um dia some. A rede **Model Fitness** quer agir antes disso. Com os perfis de 4.000
clientes, o projeto responde três perguntas: **quem tem mais chance de sair no mês seguinte**,
**que tipos de cliente existem** e **o que a academia pode fazer para segurar cada grupo**.

> **Status:** projeto aprovado na revisão da TripleTen. Seções 1 a 5 escritas, com conclusão em
> cada etapa e recomendações de retenção na seção 5.

## Estrutura do projeto

```
├── notebooks/
│   └── Sprint 13 - Churn Model Fitness.ipynb   # notebook principal
├── data/
│   └── gym_churn_us.csv        # perfis e comportamento de 4.000 clientes
├── images/                     # gráficos exportados pelo notebook
├── .gitignore
├── LICENSE                     # MIT
└── README.md
```

## Os dados

`gym_churn_us.csv`: 4.000 clientes e 14 colunas, sem ausentes nem duplicados. **1.061 clientes
(26,5%)** saíram no mês analisado.

| grupo | colunas |
|---|---|
| Perfil | `gender`, `Age`, `Near_Location`, `Partner`, `Promo_friends`, `Phone` |
| Contrato | `Contract_period` (1, 6 ou 12 meses), `Month_to_end_contract`, `Lifetime` |
| Comportamento | `Group_visits`, `Avg_class_frequency_total`, `Avg_class_frequency_current_month`, `Avg_additional_charges_total` |
| Alvo | `Churn` (1 = saiu, 0 = ficou) |

Duas diferenças em relação ao enunciado: a coluna de idade se chama `Age`, e o contrato de 3 meses
citado no enunciado não existe nos dados.

## Roteiro da análise

**Seção 1. Os dados.** Leitura, formato, tipos e conferência contra o enunciado.

**Seção 2. Análise exploratória.** Ausentes, duplicados e checagens implícitas, médias de quem saiu
e de quem ficou, distribuições por grupo e matriz de correlação com os pares de colunas quase
gêmeas.

**Seção 3. Modelo de previsão.** Divisão estratificada, linha de base "ninguém sai", regressão
logística e floresta aleatória comparadas por acurácia, precisão, sensibilidade, F1 e ROC-AUC,
teste de um limiar menor e teste de vazamento retirando a coluna suspeita.

**Seção 4. Agrupamento de clientes.** Dendrograma (ward), K-Means com 5 grupos, silhueta, perfil
médio, distribuições e taxa de saída de cada grupo.

**Seção 5. Conclusão geral e recomendações.**

## O que a análise encontrou

- **Quem sai é o cliente novo, de contrato curto e frequência em queda.** Quem saiu tinha em média
  0,99 mês de casa, contra 4,71 de quem ficou. Saíram 42,3% dos clientes de contrato mensal e 2,4%
  dos de contrato anual. Dos 122 clientes que pararam de ir no mês, 116 saíram (95,1%).
- **O modelo prevê bem acima do chute.** A regressão logística acerta 92,5% dos clientes da
  validação, contra 73,5% de um chute "ninguém sai", e encontra 83,0% dos que saem, com 88,0% de
  acerto entre os que aponta. A floresta aleatória ficou nominalmente à frente, mas dentro da margem
  declarada, e escolhi a logística por ser mais simples e mais estável sem a coluna suspeita.
- **A coluna suspeita de vazamento pesa, mas não sustenta o modelo sozinha.** Sem a frequência do
  mês corrente, a acurácia da logística cai de 92,5% para 90,1% e a sensibilidade de 83,0% para
  78,8%.
- **Os fatores que mais pesam** nos dois modelos são o tempo de casa e a frequência no mês. A idade
  pesa, mas com menos consenso entre os modelos. As colunas quase gêmeas (r = 0,97 e 0,95) chegam a
  inverter o sinal de um coeficiente da logística.
- **Cinco tipos de cliente, com taxas de saída muito diferentes.** Os de pouco vínculo (grupo 3,
  27,7% da base) perderam 52,6% dos clientes, e os que moram longe (grupo 0) perderam 45,0%. Os de
  contrato anual (grupo 1) perderam 2,2%, e os assíduos (grupo 4), 6,9%.

## Recomendações de retenção

1. **O primeiro mês decide:** boas-vindas com avaliação física e contato do instrutor no fim do
   primeiro mês.
2. **Contrato longo segura:** oferecer o semestral ou o anual com desconto no mês anterior ao fim do
   contrato mensal, começando pelos grupos de maior saída.
3. **A queda de frequência é o alarme:** rodar todo mês o modelo treinado sem a frequência do mês
   corrente (que encontra 78,8% dos que saem) e contatar os clientes com maior probabilidade de saída.
4. **Vínculo social ajuda a ficar:** convidar para aula em grupo e premiar a indicação de amigos.

Os princípios vêm de associação, não de causa, e cada ação deve ser testada antes com um grupo de
controle sorteado.

## Como rodar

1. Instale as bibliotecas: `pip install pandas numpy matplotlib seaborn scikit-learn scipy`.
2. Abra `notebooks/Sprint 13 - Churn Model Fitness.ipynb` e rode todas as células em ordem. O
   notebook procura o arquivo em `data/`, `../data/`, `/datasets/` e `/content/`.

## Ferramentas

Python · pandas · NumPy · matplotlib · seaborn · scikit-learn · SciPy
