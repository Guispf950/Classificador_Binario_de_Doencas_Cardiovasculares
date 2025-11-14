# Projeto 1: Classificação Binária de Doenças Cardíacas com Redes Neurais

**Disciplina:** Fundamentos de Inteligência Artificial (FIA)
**Professor:** Edjard Mota

---

## Sumário
* [1. Sobre o Projeto](#1-sobre-o-projeto)
* [2. Adaptações e Melhorias](#2-adaptações-e-melhorias)
* [3. Fluxo de Trabalho do Notebook](#3-fluxo-de-trabalho-do-notebook)
* [4. Resultados Obtidos](#4-resultados-obtidos)
* [5. Conclusão](#5-conclusão)

---

## 1. Sobre o Projeto

Este projeto consiste na implementação de uma Rede Neural Artificial (ANN) *feedforward* para atuar como um **classificador binário**. O objetivo é prever a presença (1) ou ausência (0) de doença cardíaca em um paciente com base em um conjunto de atributos clínicos.

### O Problema

As doenças cardiovasculares são a principal causa de morte em todo o mundo, tornando a detecção precoce um desafio crítico para a saúde pública. O desenvolvimento de modelos preditivos precisos pode auxiliar profissionais de saúde a identificar pacientes em risco, permitindo intervenções mais rápidas e eficazes.

### O Dataset

O modelo foi treinado e avaliado utilizando o dataset **Heart Disease UCI**, obtido via Kaggle. Após um rigoroso processo de limpeza, que incluiu a remoção de 723 registros duplicados, o dataset final consistiu em 302 amostras únicas.

* **Features (X):** 13 atributos clínicos, como idade, sexo, nível de colesterol (`chol`), tipo de dor no peito (`cp`) e pressão arterial (`trestbps`).
* **Target (y):** A variável-alvo (`target`), onde 0 indica ausência de doença e 1 indica a presença.

---

## 2. Adaptações e Melhorias

Este notebook foi desenvolvido com base nas instruções da disciplina (Tema1-Trabalho-RedesNeurais.pdf) e adaptado do notebook base *'Heart Disease Prediction using Neural Networks'* (disponível no Kaggle).

Foram realizadas as seguintes modificações e melhorias técnicas em relação ao código base:

1.  **Foco em Classificação Binária:** O notebook original explorava tanto a classificação categórica (múltiplos níveis de doença) quanto a binária. Conforme as instruções do trabalho, este projeto foi focado estritamente na **classificação binária** (0 vs 1), tratando qualquer diagnóstico de doença como classe "1".
2.  **Remoção de Duplicatas:** Foi identificada e executada a remoção de 723 linhas duplicadas, uma etapa de pré-processamento crucial que não estava no notebook original, garantindo a integridade e a validade estatística do modelo.
3.  **Implementação de *Early Stopping*:** Para combater o *overfitting* e otimizar o tempo de treinamento, foi adicionado um *callback* `EarlyStopping` do Keras.
    * O modelo foi configurado para rodar por até 70 épocas, monitorando a `val_accuracy`.
    * O treinamento foi interrompido automaticamente na Época 22, pois a acurácia de validação não melhorou por 15 épocas (paciência).
    * Crucialmente, a opção `restore_best_weights=True` foi ativada, garantindo que o modelo final utilizado para avaliação fosse aquele com os pesos da **Época 7**, que apresentou o melhor desempenho de generalização.

---

## 3. Fluxo de Trabalho do Notebook

O notebook segue o fluxo padrão de um projeto de *Deep Learning*:

1.  **Carga e Limpeza:** Os dados são carregados via API do Kaggle e limpos (remoção de 723 duplicatas).
2.  **Análise Exploratória (EDA):** Verificação de tipos de dados, balanceamento de classe (54% vs 46%, bem balanceado) e análise de correlação.
3.  **Pré-processamento:**
    * Divisão dos dados em treino (80%) e teste (20%).
    * **Normalização (Padronização):** Aplicação do `StandardScaler` *após* a divisão (fit no treino, transform no teste) para evitar *data leakage*. Este passo é essencial devido às diferentes escalas das *features* (ex: `age` vs `chol`).
4.  **Construção do Modelo (Keras):** A ANN foi construída com:
    * Camada de Entrada (13 neurônios)
    * 2 Camadas Ocultas (16 e 8 neurônios) com ativação **ReLU**.
    * Regularização **Dropout** (0.25) e **L2** para prevenir *overfitting*.
    * Camada de Saída (1 neurônio) com ativação **Sigmoid** para a probabilidade binária.
5.  **Treinamento:** O modelo foi compilado com *loss* `binary_crossentropy` e otimizador `adam`, e treinado com *Early Stopping*.
6.  **Avaliação:** O desempenho do modelo foi medido no conjunto de teste.

---

## 4. Resultados Obtidos

A avaliação do modelo no conjunto de teste (61 amostras) revelou um desempenho robusto e, o mais importante, clinicamente relevante.

### Métricas de Performance

| Métrica | Resultado (Teste) | Interpretação |
| :--- | :--- | :--- |
| **Acurácia** | **81.97%** | O modelo acertou a classificação de 50 dos 61 pacientes. |
| **Precisão (Classe 1)** | **78.95%** | Das vezes que o modelo previu "Com Doença", ele acertou 79% das vezes. |
| **Recall (Classe 1)** | **90.91%** | O modelo identificou corretamente 91% de todos os pacientes que **realmente** tinham a doença. |

### Matriz de Confusão

A matriz de confusão detalha os acertos e erros:

* **Verdadeiros Positivos (TP): 30**
    * O modelo acertou 30 pacientes que tinham doença.
* **Verdadeiros Negativos (TN): 20**
    * O modelo acertou 20 pacientes que *não* tinham doença.
* **Falsos Negativos (FN): 3**
    * O modelo **errou 3 pacientes** que tinham doença, mas foram classificados como saudáveis.
* **Falsos Positivos (FP): 8**
    * O modelo errou 8 pacientes que eram saudáveis, mas foram classificados como doentes.

---

## 5. Conclusão

O resultado mais importante é o **Recall de 90.91%**. Em um cenário de diagnóstico médico, é muito mais grave cometer um Falso Negativo (não detectar a doença) do que um Falso Positivo.

O modelo demonstrou ser altamente sensível, minimizando o erro mais perigoso (apenas 3 Falsos Negativos). A acurácia final de **81.97%** no conjunto de teste, combinada com a baixa diferença entre as curvas de treino e validação (graças ao *Dropout* e *Early Stopping*), indica que o modelo generaliza bem para dados novos.

A normalização dos dados foi um passo fundamental; sem ela, as *features* com grandes magnitudes (como `chol`) teriam impedido o otimizador `adam` de convergir para uma solução eficaz.
