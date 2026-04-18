# Opis projektu
Skrypt w języku Python do analizy danych dotyczących wypłaconych ubezpieczeń samochodowych. Skrypt przeprowadza analize danych wizualizując rozkłady wypłaconych odszkodowań oraz korzysta z Uogólnionego Modelu Liniowego (GLM), który próbuje przewidzieć ile będzie wynosić wypłacone odszkodowanie pod warunkiem że doszło do kolizji, gdzie odszkodowanie przekracza 25 000 USD, a zmienne objaśniające to wiek, płeć oraz marka samochodu. Skrypt odpowiada też na pytanie czy powyższe parametry w ogóle mają znaczenie statystyczne określając **p-value**.

# Zbiór danych
Skrypt korzysta ze zbioru danych dostępnego na stronie https://www.kaggle.com/datasets/buntyshah/auto-insurance-claims-data

# Stack
- pandas
- seaborn
- matplotlib
- statsmodels

# Działanie skryptu krok po kroku
1. **Wczytanie danych:** Pobiera dane z pliku `insurance_claims.csv`, a następnie grupuje ubezpieczonych na 5 kategorii wiekowych.
2. **Mapa cieplna:** Tworzy tabelę przestawną i wizualizację prezentującą medianę wartości wypłaconych odszkodowań w rozbiciu na markę samochodu, płeć oraz grupę wiekową.<img width="1400" height="800" alt="Figure_1" src="https://github.com/user-attachments/assets/1d61fb09-8324-4539-b7a8-1e574e5d327c" />

3. **Analiza rozkładu zmiennej:** Rysuje histogram całej populacji odszkodowań, aby zwizualizować jej rozkład. Obserwujemy dwa szczyty.<img width="1000" height="600" alt="Figure_2" src="https://github.com/user-attachments/assets/670be4b4-a887-49ff-b561-544c7de89d33" />
4. **Filtrowanie dużych szkód:** Odrzuca niewielkie wypłaty, izolując do dalszej analizy tylko duże szkody powyżej 25 000 USD.<img width="1000" height="600" alt="Figure_3" src="https://github.com/user-attachments/assets/b7f10e9c-3430-4b32-b4e7-83f157b5c578" />
5. **Modelowanie (GLM):** na wyodrębnionym prawym piku rozkładu danych trenujemy model regresji wykorzystujący rozkład Gamma z logarytmiczną funkcją łączącą. Model przewiduje wielkość roszczenia na podstawie:
    - **typu kolizji**
    - **Ilości doznanych obrażeń**
    - **ilości biorących udział w wypadku pojazdów**
    - **Wieku** ubezpieczonego wraz z `age**2`, aby uwzględnić możliwą nieliniowość (młodzi kierowcy mogą jeździć niebezpiecznie, ci w średnim wieku bardziej opanowanie, a ci starsi mieć więcej wypadków ze względu wiek)
    - **Płci**
    - **Marki** samochodu
6. **Wyświetla podsumowanie modelu:** <img width="800" height="500" alt="Figure_4" src="https://github.com/user-attachments/assets/bead0725-de93-4bd8-8e8e-f03ab2ecdb67" />


```
                 Generalized Linear Model Regression Results
==============================================================================
Dep. Variable:               nadwyzka   No. Observations:                  818
Model:                            GLM   Df Residuals:                      797
Model Family:                   Gamma   Df Model:                           20
Link Function:                    Log   Scale:                         0.16840
Method:                          IRLS   Log-Likelihood:                -9094.4
Date:                Sat, 18 Apr 2026   Deviance:                       174.73
Time:                        15:09:41   Pearson chi2:                     134.
No. Iterations:                    11   Pseudo R-squ. (CS):            0.03316
Covariance Type:            nonrobust
=======================================================================================================
                                          coef    std err          z      P>|z|      [0.025      0.975]
-------------------------------------------------------------------------------------------------------
Intercept                              10.6522      0.237     45.018      0.000      10.188      11.116
C(collision_type)[T.Rear Collision]    -0.0685      0.036     -1.915      0.055      -0.139       0.002
C(collision_type)[T.Side Collision]    -0.0282      0.036     -0.777      0.437      -0.099       0.043
C(insured_sex)[T.MALE]                 -0.0221      0.029     -0.763      0.445      -0.079       0.035
C(auto_make)[T.Audi]                    0.0590      0.078      0.760      0.447      -0.093       0.211
C(auto_make)[T.BMW]                     0.0795      0.076      1.043      0.297      -0.070       0.229
C(auto_make)[T.Chevrolet]               0.0563      0.076      0.739      0.460      -0.093       0.205
C(auto_make)[T.Dodge]                   0.0727      0.074      0.979      0.328      -0.073       0.218
C(auto_make)[T.Ford]                    0.0987      0.076      1.293      0.196      -0.051       0.248
C(auto_make)[T.Honda]                  -0.0058      0.082     -0.070      0.944      -0.167       0.156
C(auto_make)[T.Jeep]                    0.0254      0.079      0.323      0.747      -0.129       0.180
C(auto_make)[T.Mercedes]                0.0571      0.080      0.715      0.474      -0.099       0.213
C(auto_make)[T.Nissan]                 -0.0332      0.075     -0.442      0.658      -0.180       0.114
C(auto_make)[T.Saab]                    0.0035      0.075      0.046      0.963      -0.144       0.151
C(auto_make)[T.Suburu]                 -0.0039      0.074     -0.053      0.958      -0.149       0.141
C(auto_make)[T.Toyota]                 -0.0268      0.079     -0.339      0.735      -0.182       0.128
C(auto_make)[T.Volkswagen]             -0.0293      0.078     -0.375      0.708      -0.183       0.124
bodily_injuries                         0.0413      0.017      2.364      0.018       0.007       0.076
number_of_vehicles_involved            -0.0298      0.014     -2.123      0.034      -0.057      -0.002
age                                    -0.0059      0.011     -0.520      0.603      -0.028       0.016
I(age ** 2)                             0.0001      0.000      0.744      0.457      -0.000       0.000
=======================================================================================================
```

# Wnioski z modelu
Wartości $P>|z|$ dla zmiennych objaśniających: płeć, marka samochodu, wiek są znacznie wyższe niż $\alpha = 0.05$ nie ma więc powodu, aby odrzucać hipotezę zerową. Oznacza to, że w analizowanej próbce, ani płeć, ani marka samochodu, ani wiek nie wykazują istotnego statystycznego wpływu na wysokość największych odszkodowań. Natomiast dla **typu kolizji, liczby rannych i liczby biorących udział w wypadku samochodów** P - wartość jest na granicy istotności lub ją osiągneła.
```
- C(collision_type)[T.Rear Collision]    P - value: 0.055 ~= 0.05
- bodily_injuries                        P - value: 0.018 < 0.05
- number_of_vehicles_involved            P - value: 0.034 < 0.05
```
Spójrzmy na współczynnik przy kolizji od tyłu wynosi on -0.0685, oznacza to ze jesli doszlo do kolizji tylnej dodajemy do logarytmu wyniku -0.0685 wiec wyplacone odszkodowanie bedzie przemnożone przez: $e^{-0.0685}=0.9337935$ więc będzie około 6% tańsze w porównaniu do kolizji przedniej. Dla kolizji bocznej p - value wynosi 0.437 wiec nie ma ona istotnego statystycznego wpływu.

---

Dla ilości rannych współczynnik wynosi 0.0413, więc dla 0 rannych osób wynik mnożymy przez 1 (wartość bazowa), dla jednej osoby mamy 0.0413*1, wiec wynik mnozymy przez $e^{0.0413}=1.042$ (wynik rośnie o 4,2%). Analogicznie dla dwóch rannych wynik rośnie o 8,6% względem wypadku bez rannych, dla 3 osób to już 13,2%.

---

Dla ilości pojazdów biorących udział w wypadku współczynnik jest ujemy -0.0298. Jest to interesujące, ponieważ oznacza, że każdy kolejny pojazd obniża wartość wypłaconego odszkodowania. Wypadek z jednym autem $e^{-0.0298}=0.9706$. Dla 3 aut mnożnik wynosi $e^{-0.0298 \cdot 3}=0.9144$, czyli wypadek jest tańszy o 6% względem wypadku z jednym autem.
