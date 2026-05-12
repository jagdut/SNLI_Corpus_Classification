# SNLI Corpus Classification using SNLI Dataset
Projekt dotyczący klasyfikacji par zdań pod kątem ich relacji logicznej.

## Opis projektu
Projekt polegał na klasyfikacji pary zdań — przesłanki oraz hipotezy — do jednej z trzech kategorii:
* **entailment** (wynikanie): drugie zdanie jest konsekwencją pierwszego,
* **contradiction** (sprzeczność): drugie zdanie jest zaprzeczeniem pierwszego,
* **neutral** (neutralność): drugie zdanie jest neutralne wobec pierwszego.

## Dane (SNLI Corpus)
Dane pobrano z korpusu SNLI (Stanford Natural Language Inference - https://nlp.stanford.edu/projects/snli/). Są to dane napisane przez ludzi i także przez nich sklasyfikowane. Każdy rekord w tych danych składa się z dwóch części- zdania przesłanki a następnie zdania hipotezy. Każdemu rekordowi jest przypisany charakter relacji między tymi zdaniami (entailment, contradiction lub neutral). Charakter każdej z pary zdań został określony przez ludzi i każda para dostała 5 głosów. Wśród danych występuje kolumna „gold_label” która określa która kategoria była dominantą dla danej pary (w przypadku braku dominanty, przypisany jest znak „-”). Rekordy o przypisanym gold_label „-” usunięto i nie brano ich pod uwagę podczas wykonywania zadania.


### Liczność zbiorów danych
| Kategoria | Zbiór treningowy | Zbiór walidacyjny | Zbiór testowy |
| :--- | :--- | :--- | :--- |
| **entailment** | 183 416 | 3 329 | 3 368 |
| **contradiction** | 183 187 | 3 278 | 3 237 |
| **neutral** | 182 764 | 3 235 | 3 219 |
| **łącznie** | **549 367** | **9 842** | **9 824** |

## Architektura i proces dochodzenia do najlepszego modelu
Eksperymenty przeprowadzono w środowisku Google Colab. Przetestowano trzy warianty architektury opartej na sieciach biLSTM, która pozwala na dwustronną analizę zależności między zdaniami.

### Testowanie różnych modeli:
1.  **Model v1:** Prosty model liniowy, biLSTM (hidden=128), lr=0.01 $\rightarrow$ **68%** accuracy (val).
2.  **Model v2 (najlepszy):** Dodano Batch Normalization, ReLU oraz Dropout (0.1), lr=0.001 $\rightarrow$ **78%** accuracy (val).
3.  **Model v3:** Zmiana na LeakyReLU i zwiększony Dropout (0.3) $\rightarrow$ **77%** accuracy (val).

### Architektura najlepszego modelu (v2):
* **embeddingi:** GloVe 50D,
* **RNN:** biLSTM, hidden_size=128, dwukierunkowy,
* **łączenie danych:** Konkatenacja wektorów przesłanki i hipotezy,
* **klasyfikator:** Linear $\rightarrow$ BatchNorm $\rightarrow$ ReLU $\rightarrow$ Dropout(0.1) $\rightarrow$ Linear(3),
* **trening:** Optymalizator Adam, lr=0.001, 10 epok.

## Wyniki najlepszego modelu (v2)
Model uzyskał **80%** dokładności (accuracy) na zbiorze testowym.

| Klasa | Precision | Recall | F1-score | Support |
| :--- | :--- | :--- | :--- | :--- |
| **entailment** | 0.79 | 0.86 | 0.82 | 3 368 |
| **contradiction** | 0.83 | 0.80 | 0.82 | 3 237 |
| **neutral** | 0.78 | 0.73 | 0.75 | 3 219 |
| **accuracy** | | | **0.80** | **9 824** |
| **macro avg** | 0.80 | 0.80 | 0.80 | 9824 |
| **weighted avg** | 0.80 | 0.80 | 0.80 | 9824 |

## Przykłady pary zdań do testowania najlepszego modelu
* **Przesłanka:** *I do not like jazz.* ; **Hipoteza:** *When I was younger I used to sleep longer.* ; **Predykcja:** *Contradiction* $\rightarrow$ **NIEPOPRAWNE**
* **Przesłanka:** *You look hungry.* ; **Hipoteza:** *You should eat something.* ; **Predykcja:** *Entailment* $\rightarrow$ **POPRAWNE**
* **Przesłanka:** *Gardening is what makes me happy.* ; **Hipoteza:** *All those trees and flowers, I hate them.* ; **Predykcja:** *Contradiction*  $\rightarrow$ **POPRAWNE**

**Wykorzystane technologie:** PyTorch, Torchtext, Python, Jupyter Notebook, Google Colab.
