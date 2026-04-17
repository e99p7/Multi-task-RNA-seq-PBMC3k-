**🧬 Multi-task Learning on Single-Cell RNA-seq (PBMC3k)**

**Overview**

This project explores **representation learning on single-cell RNA-seq data** using multi-task neural networks.

**The goal is to learn a latent space that captures:**

* cell identity (discrete structure)
* biological programs (continuous signals):

  * stress response
  * immune activity
  * apoptosis
  * proliferation

The project evolved through two main stages, improving both modeling strategy and biological validity.



**Dataset**

* **Source:** `scanpy.datasets.pbmc3k()`
* **Type:** single-cell RNA-seq
* **Samples:** ~3,000 cells
* **Genes:** ~7,000 genes



**Methodology**

Preprocessing

* log-transform: `log1p`
* gene-wise standardization
* PCA (dimensionality reduction for model input)

**Gene Set Scoring**

Biological signals are approximated using gene sets:

* **Stress:** HIF1A, HSPA1A, DNAJB1, ATF4, DDIT3
* **Immune:** CD4, CD8A, IFNG, IL2RA, STAT1
* **Apoptosis:** BAX, CASP3, TP53, CASP8, BCL2



**Variant 1 — Multi-task Model with Cell-Type Supervision**

Idea

Introduce **multi-task learning**:

* Regression → biological signals
* Classification → cell type (Leiden clusters)

**Model**

* MLP encoder
* latent embedding (`z`)
* heads:

  * stress
  * immune
  * apoptosis
  * tert / proxy
  * classification (cell type)

**Key Insight**

Adding classification:

* stabilizes training
* structures latent space
* aligns embedding with biological clusters

**Result**

Latent space shows:

* clear separation by cell type
* improved interpretability



**Variant 2 — Final Model (Proliferation вместо TERT)**

Problem

In PBMC3k:

```
TERT expression is weak or absent
```

**Using TERT leads to:**

* noisy supervision
* weak biological signal

**Solution**

Replace TERT with **proliferation gene signature**:

* MKI67
* TOP2A
* PCNA
* TYMS
* MCM2
* MCM5
* CDK1
* UBE2C

**Final Tasks**

Regression

* stress
* immune
* apoptosis
* proliferation

**Classification**

* cell type (Leiden clusters)



**Final Model Architecture**

* encoder (MLP)
* latent space `z` (16-dim)
* multi-head outputs:

  * 4 regression heads
  * 1 classification head



**Results**

Training

* stable convergence
* balanced multi-task losses
* good validation accuracy

**Latent Space**

Two complementary views:

1. **Cell type coloring (Leiden)**

   * clear clustering
   * discrete structure

2. **Proliferation coloring**

   * smooth gradient
   * continuous biological signal

**Interpretation**

The model captures both:

* **discrete structure** → cell identity
* **continuous structure** → proliferation programs



**Key Takeaway**

This project demonstrates an important principle:

> Good ML models in biology depend on choosing biologically meaningful targets.

Replacing TERT with proliferation:

* improved signal quality
* improved interpretability
* made the model biologically consistent



**Tech Stack**

* Python
* PyTorch
* Scanpy
* scikit-learn
* UMAP
* Matplotlib


**Future Work**

* Apply pipeline to **cancer datasets (TCGA)** where TERT is relevant
* Add **gene interaction priors / GNN**
* Explore **causal modeling (gene → pathway)**
* Add **feature attribution (gene importance)**



**Repository Structure**

```
notebooks/
  01_multitask_celltype.ipynb
  02_final_proliferation_model.ipynb
```



**Conclusion**

The final model is not only technically stronger but also biologically more meaningful.

It shows how combining:

* multi-task learning
* proper biological targets
* single-cell data

can produce interpretable latent representations.


---

**🧬 Multi-task обучение на данных single-cell RNA-seq (PBMC3k)**

Обзор

В этом проекте исследуется **обучение представлений (representation learning)** на данных single-cell RNA-seq с использованием multi-task нейронных сетей.

Цель — построить латентное пространство, которое одновременно отражает:

* **тип клетки** (дискретная структура)
* **биологические процессы** (непрерывные сигналы):

  * стресс
  * иммунная активность
  * апоптоз
  * пролиферация

Проект развивался поэтапно — от базовой модели к более биологически корректной постановке задачи.


**Данные**

* **Источник:** `scanpy.datasets.pbmc3k()`
* **Тип:** single-cell RNA-seq
* **Количество клеток:** ~3000
* **Количество генов:** ~7000

---

**Методология**

Предобработка

* логарифмирование: `log1p`
* стандартизация по генам
* PCA (снижение размерности для входа модели)


**Биологические сигналы (gene sets)**

Используются наборы генов для приближённой оценки биологических процессов:

* **Стресс:** HIF1A, HSPA1A, DNAJB1, ATF4, DDIT3
* **Иммунный ответ:** CD4, CD8A, IFNG, IL2RA, STAT1
* **Апоптоз:** BAX, CASP3, TP53, CASP8, BCL2



**Вариант 1 — Multi-task модель с классификацией типов клеток**

Идея

Добавить multi-task обучение:

* **Регрессия** → биологические сигналы
* **Классификация** → тип клетки (кластеризация Leiden)

**Архитектура**

* MLP encoder
* латентное пространство (`z`)
* выходы:

  * stress
  * immune
  * apoptosis
  * tert / proxy
  * классификация (cell type)


**Основной инсайт**

Добавление classification-задачи:

* стабилизирует обучение
* делает латентное пространство структурированным
* улучшает интерпретируемость


**Результат**

Латентное пространство:

* хорошо разделяет типы клеток
* становится биологически осмысленным


**Вариант 2 — Финальная модель (замена TERT на пролиферацию)**

Проблема

В датасете PBMC3k:

```
TERT практически не выражен
```

**Это приводит к:**

* слабому сигналу
* шумной регрессии
* плохой интерпретации


**Решение**

Использовать **пролиферацию как биологический proxy**

Гены:

* MKI67
* TOP2A
* PCNA
* TYMS
* MCM2
* MCM5
* CDK1
* UBE2C


**Финальные задачи**

Регрессия:

* stress
* immune
* apoptosis
* proliferation

**Классификация:**

* тип клетки (Leiden)



**Архитектура модели**

* encoder (MLP)
* латентное пространство `z` (16 измерений)
* multi-head выход:

  * 4 regression head
  * 1 classification head


**Результаты**

Обучение

* стабильная сходимость
* сбалансированные функции потерь
* хорошая accuracy по типам клеток



**Латентное пространство**

Два ключевых представления:

1. По типам клеток (Leiden)

* чёткое разделение кластеров
* дискретная структура

2. По пролиферации

* плавный градиент
* непрерывный биологический сигнал



**Интерпретация**

Модель одновременно захватывает:

* **дискретную структуру** → тип клетки
* **непрерывную структуру** → пролиферация



**Ключевой вывод**

> В биоинформатике важна не только архитектура модели, но и правильный выбор биологических таргетов.

Замена TERT на пролиферацию:

* улучшила качество сигнала
* сделала модель интерпретируемой
* повысила биологическую корректность



**Стек технологий**

* Python
* PyTorch
* Scanpy
* scikit-learn
* UMAP
* Matplotlib



**Дальнейшие направления**

* переход на **TCGA (онкологические данные)**, где TERT релевантен
* добавление **GNN / gene interaction**
* causal modeling (ген → путь)
* интерпретация модели (важность генов)



**Структура проекта**

```
notebooks/
  01_multitask_celltype.ipynb
  02_final_proliferation_model.ipynb
```


**Заключение**

Проект демонстрирует полный цикл разработки ML-модели в биологии:

1. базовая модель
2. улучшение через multi-task обучение
3. корректировка биологической гипотезы

Финальная модель является не только технически более сильной, но и биологически более обоснованной.
