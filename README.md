# Esteira de ML: triagem de risco de diabetes

Trabalho da disciplina de Inteligência Artificial (FATEC Praia Grande, Análise e Desenvolvimento de Sistemas).

## Descrição do projeto

Uma esteira completa de aprendizado de máquina em um único notebook Python, da preparação dos dados até a predição com o modelo salvo.

- **Problema:** classificação binária. A partir de medidas simples (glicose, pressão, IMC, idade, histórico familiar etc.), prever se a paciente **tem diabetes** (1) ou **não** (0).
- **Aplicação:** triagem em saúde, para priorizar quem deve fazer exames mais completos.
- **Base de dados:** [Pima Indians Diabetes Database](https://www.kaggle.com/datasets/rishabhm76/pima-diabetes-database) (Kaggle), com 768 pacientes, 8 colunas de entrada e a coluna alvo.

### Etapas da esteira

| #   | Etapa                    | Resumo                                                                                                                                                                                                     |
| --- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Carga                    | Leitura do `diabetes.csv` (baixado automaticamente se não existir)                                                                                                                                         |
| 2   | Estatísticas descritivas | Resumo numérico, balanceamento do alvo, gráficos, e a descoberta de que zeros em glicose, pressão, insulina e IMC são valores faltantes disfarçados                                                        |
| 3   | Transformação de colunas | Zeros impossíveis viram valor faltante, `log(1 + x)` em `DiabetesPedigreeFunction`, criação de `faixa_imc` (faixas da OMS), remoção de `SkinThickness`                                                     |
| 4   | Transformação de linhas  | Remoção de duplicadas e de linhas sem glicose, pressão ou IMC                                                                                                                                              |
| 5   | Divisão dos dados        | Treino 70%, validação 15%, teste 15%, estratificada pela classe alvo                                                                                                                                       |
| 6   | Treino                   | Pré-processamento ajustado só no treino (imputação, escala, one-hot) e comparação de Regressão Logística, Random Forest e Gradient Boosting contra um baseline, com ajuste de hiperparâmetros na validação |
| 7   | Avaliação                | Matriz de confusão, acurácia, precisão, recall, F1, ROC AUC e intervalo de confiança da acurácia (bootstrap) no conjunto de teste                                                                          |
| 8   | Implantação              | Modelo salvo com `joblib`, recarregado e usado para prever duas pacientes novas                                                                                                                            |

### Resultados (execução com `SEED = 42`)

| Item                 | Valor                                                |
| -------------------- | ---------------------------------------------------- |
| Modelo escolhido     | Random Forest ajustado                               |
| Acurácia no teste    | 71,6% (intervalo de confiança de 95%: 63,3% a 79,8%) |
| Acurácia do baseline | 66,1%                                                |
| Recall da classe 1   | 0,54                                                 |
| Precisão da classe 1 | 0,59                                                 |
| F1 da classe 1       | 0,56                                                 |
| ROC AUC              | 0,77                                                 |

Matriz de confusão no teste (109 pacientes): 58 verdadeiros negativos, 14 falsos positivos, 17 falsos negativos e 20 verdadeiros positivos.

O modelo supera o baseline, mas o recall é baixo: cerca de 46% das pacientes com diabetes não foram identificadas no teste. Em triagem de saúde esse é o erro mais grave, e o próximo passo seria ajustar o limiar de decisão para priorizar recall. Como a base é pequena, os números variam um pouco entre versões das bibliotecas.

## Estrutura do repositório

```
.
├── esteira_ml_diabetes.ipynb   # notebook com toda a esteira (com as saídas da execução)
├── requirements.txt            # dependências
├── data/
│   ├── LEIAME.md               # sobre a base
│   └── diabetes.csv            # base de dados
├── figuras/
│   └── matriz_confusao.png     # gerada pelo notebook
└── modelos/                    # gerada pelo notebook (modelo salvo, não versionada)
```

## Como reproduzir a execução do vídeo

Pré-requisitos: Python 3.10 ou superior e Git.

**1. Clone o repositório**

```bash
git clone https://github.com/gandralice/esteira-ml-diabetes.git
cd esteira-ml-diabetes
```

**2. Crie e ative um ambiente virtual**

```bash
python -m venv .venv
# Linux e macOS
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1
```

**3. Instale as dependências**

```bash
pip install -r requirements.txt
```

**4. Abra o notebook**

```bash
jupyter lab esteira_ml_diabetes.ipynb
```

**5. Execute tudo**

No menu: _Kernel > Restart Kernel and Run All Cells_. A execução leva menos de um minuto. A base já está em `data/diabetes.csv`. Se o arquivo não existir, o notebook baixa sozinho (precisa de internet).

**6. O que você deve ver**

- Estatísticas e gráficos da base, incluindo a tabela de zeros impossíveis
- Quantas linhas foram removidas na limpeza (44 de 768)
- Tabela comparando os modelos na validação
- Matriz de confusão e acurácia no teste, com intervalo de confiança
- Predição para duas pacientes de exemplo
- Arquivos gerados: `figuras/matriz_confusao.png` e `modelos/modelo_final.joblib`

Como há semente fixa (`SEED = 42`), os conjuntos e resultados se repetem a cada execução com as mesmas versões das bibliotecas.

### Alternativa: Google Colab

1. Abra o notebook no Colab (_File > Upload notebook_).
2. Execute com _Runtime > Run all_. As bibliotecas usadas já vêm instaladas no Colab e a base é baixada sozinha.

## Limitações

- Base pequena (cerca de 700 pacientes depois da limpeza): validação e teste ficam com pouco mais de 100 pacientes cada, então os resultados oscilam bastante.
- População específica: mulheres com 21 anos ou mais, de origem Pima. O modelo não vale para outras populações.
- Os zeros impossíveis foram tratados como faltantes, uma interpretação razoável mas não confirmada pelos autores da base.
- Uma única divisão treino/validação/teste. Validação cruzada daria resultados mais estáveis.
- Exercício acadêmico, não é diagnóstico médico e não deve ser usado para decidir nada sobre uma pessoa real.

## Autora

Alice, estudante de ADS na FATEC Praia Grande.
