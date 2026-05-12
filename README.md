# SNLI Corpus Classification using SNLI Dataset
Projekt dotyczący klasyfikacji par zdań pod kątem ich relacji logicznej.

## Opis projektu
Projekt polegał na klasyfikacji pary zdań — **przesłanki** oraz **hipotezy** — do jednej z trzech kategorii:
* **Entailment** (wynikanie): drugie zdanie jest konsekwencją pierwszego[cite: 7].
* **Contradiction** (sprzeczność): drugie zdanie jest zaprzeczeniem pierwszego[cite: 7].
* **Neutral** (neutralność): drugie zdanie jest neutralne wobec pierwszego[cite: 7].

## Dane (SNLI Corpus)
Dane pobrano z korpusu SNLI (Stanford Natural Language Inference). Są to dane napisane przez ludzi i także przez nich sklasyfikowane. Każdy rekord w tych danych składa się z dwóch części- zdania przesłanki a następnie zdania hipotezy. Każdemu rekordowi jest przypisany charakter relacji między tymi zdaniami tj. czy drugie jest konsekwencją pierwszego (entailment), czy drugie jest zaprzeczeniem pierwszego (contradiction) oraz czy drugie jest neutralne wobec pierwszego (neutral). Charakter każdej z pary zdań został określony przez ludzi i każda para dostała 5 głosów. Wśród danych występuje kolumna „gold_label” która określa która kategoria była dominantą dla danej pary (w przypadku braku dominanty, przypisany jest znak „-”). Rekordy o przypisanym gold_label „-” usunięto i nie brano ich pod uwagę podczas wykonywania zadania.


### Liczność zbiorów danych
| Kategoria | Zbiór treningowy | Zbiór walidacyjny | Zbiór testowy |
| :--- | :--- | :--- | :--- |
| **Entailment** | 183 416 | 3 329 | 3 368 |
| **Contradiction** | 183 187 | 3 278 | 3 237 |
| **Neutral** | 182 764 | 3 235 | 3 219 |
| **Łącznie** | **549 367** | **9 842** | **9 824** |

## Architektura i proces dochodzenia do najlepszego modelu
Eksperymenty przeprowadzono w środowisku Google Colab. Przetestowano trzy warianty architektury opartej na sieciach **biLSTM**, która pozwala na dwustronną analizę zależności między zdaniami.

### Testowanie różnych modeli:
1.  **Model v1:** Prosty model liniowy, biLSTM (hidden=128), lr=0.01 $\rightarrow$ **68%** accuracy (val).
2.  **Model v2 (Najlepszy):** Dodano Batch Normalization, ReLU oraz Dropout (0.1), lr=0.001 $\rightarrow$ **78%** accuracy (val).
3.  **Model v3:** Zmiana na LeakyReLU i zwiększony Dropout (0.3) $\rightarrow$ **77%** accuracy (val).

### Architektura najlepszego modelu (v2):
* **Embeddingi:** GloVe 50D.
* **RNN:** biLSTM, `hidden_size=128`, dwukierunkowy.
* **Łączenie danych:** Konkatenacja wektorów przesłanki i hipotezy.
* **Klasyfikator:** Linear $\rightarrow$ BatchNorm $\rightarrow$ ReLU $\rightarrow$ Dropout(0.1) $\rightarrow$ Linear(3).
* **Trening:** Optymalizator Adam, lr=0.001, 10 epok.

## Wyniki najlepszego modelu (v2)
Model uzyskał **80%** dokładności (accuracy) na zbiorze testowym.

| Klasa | Precision | Recall | F1-score | Support |
| :--- | :--- | :--- | :--- | :--- |
| **Entailment** | 0.79 | 0.86 | 0.82 | 3 368 |
| **Contradiction** | 0.83 | 0.80 | 0.82 | 3 237 |
| **Neutral** | 0.78 | 0.73 | 0.75 | 3 219 |
| **Accuracy** | | | **0.80** | **9 824** |

## Przykłady pary zdań do testowania najlepszego modelu
> **Przesłanka:** *I do not like jazz.* > **Hipoteza:** *When I was younger I used to sleep longer.* > **Predykcja:** **Contradiction** $\rightarrow$ **NIEPOPRAWNE**
> **Przesłanka:** *You look hungry.* > **Hipoteza:** *You should eat something.* > **Predykcja:** **Entailment** $\rightarrow$ **POPRAWNE**
> **Przesłanka:** *Gardening is what makes me happy.* > **Hipoteza:** *All those trees and flowers, I hate them.* > **Predykcja:** **Contradiction**  $\rightarrow$ **POPRAWNE**

**Wykorzystane technologie:** PyTorch, Torchtext, Python, Jupyter Notebook, Google Colab.
