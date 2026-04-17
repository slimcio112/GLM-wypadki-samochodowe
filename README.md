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
2. **Mapa cieplna:** Tworzy tabelę przestawną i wizualizację prezentującą medianę wartości wypłaconych odszkodowań w rozbiciu na markę samochodu, płeć oraz grupę wiekową.<img width="1400" height="800" alt="Figure_1" src="https://github.com/user-attachments/assets/a0385f92-ada5-4d58-8b89-97a2d493570e" />
3. **Analiza rozkładu zmiennej:** Rysuje histogram całej populacji odszkodowań, aby zwizualizować jej rozkład. Obserwujemy dwa szczyty.<img width="1000" height="600" alt="Figure_2" src="https://github.com/user-attachments/assets/670be4b4-a887-49ff-b561-544c7de89d33" />
4. **Filtrowanie dużych szkód:** Odrzuca niewielkie wypłaty, izolując do dalszej analizy tylko duże szkody powyżej 25 000 USD.<img width="1000" height="600" alt="Figure_3" src="https://github.com/user-attachments/assets/b7f10e9c-3430-4b32-b4e7-83f157b5c578" />
5. **Modelowanie (GLM):** na wyodrębnionym prawym piku rozkładu danych trenujemy model regresji wykorzystujący rozkład Gamma z logarytmiczną funkcją łączącą. Model przewiduje wielkość roszczenia na podstawie:
    - **Wieku** ubezpieczonego wraz z `age**2`, aby uwzględnić możliwą nieliniowość (młodzi kierowcy mogą jeździć niebezpiecznie, ci w średnim wieku bardziej opanowanie, a ci starsi mieć więcej wypadków ze względu wiek)
    - **Płci**
    - **Marki** samochodu
6. **Wyświetla podsumowanie modelu:** <img width="800" height="500" alt="Figure_4" src="https://github.com/user-attachments/assets/1d75efab-7e93-4d2d-ac00-fdcf38e8288a" />

```
                 Generalized Linear Model Regression Results
==============================================================================
Dep. Variable:               nadwyzka   No. Observations:                  818
Model:                            GLM   Df Residuals:                      801
Model Family:                   Gamma   Df Model:                           16
Link Function:                    Log   Scale:                         0.17021
Method:                          IRLS   Log-Likelihood:                -9100.4
Date:                Sat, 18 Apr 2026   Deviance:                       177.11
Time:                        01:14:51   Pearson chi2:                     136.
No. Iterations:                    10   Pseudo R-squ. (CS):            0.01620
Covariance Type:            nonrobust
==============================================================================================
                                 coef    std err          z      P>|z|      [0.025      0.975]
----------------------------------------------------------------------------------------------
Intercept                     10.5919      0.235     44.988      0.000      10.130      11.053
C(insured_sex)[T.MALE]        -0.0261      0.029     -0.895      0.371      -0.083       0.031
C(auto_make)[T.Audi]           0.0609      0.078      0.783      0.433      -0.091       0.213
C(auto_make)[T.BMW]            0.0753      0.077      0.984      0.325      -0.075       0.225
C(auto_make)[T.Chevrolet]      0.0530      0.076      0.694      0.488      -0.097       0.203
C(auto_make)[T.Dodge]          0.0660      0.074      0.886      0.376      -0.080       0.212
C(auto_make)[T.Ford]           0.0986      0.077      1.288      0.198      -0.051       0.249
C(auto_make)[T.Honda]         -0.0017      0.083     -0.020      0.984      -0.164       0.160
C(auto_make)[T.Jeep]           0.0125      0.079      0.159      0.874      -0.142       0.167
C(auto_make)[T.Mercedes]       0.0658      0.080      0.822      0.411      -0.091       0.223
C(auto_make)[T.Nissan]        -0.0473      0.075     -0.628      0.530      -0.195       0.100
C(auto_make)[T.Saab]          -0.0043      0.076     -0.056      0.955      -0.152       0.144
C(auto_make)[T.Suburu]        -0.0093      0.074     -0.125      0.900      -0.155       0.137
C(auto_make)[T.Toyota]        -0.0350      0.080     -0.440      0.660      -0.191       0.121
C(auto_make)[T.Volkswagen]    -0.0188      0.078     -0.240      0.810      -0.172       0.135
age                           -0.0055      0.011     -0.476      0.634      -0.028       0.017
I(age ** 2)                 9.917e-05      0.000      0.719      0.472      -0.000       0.000
==============================================================================================
```

# Wnioski z modelu
Wartości $P>|z|$ dla wszystkich zmiennych objaśniających są znacznie wyższe niż $\alpha = 0.05$ nie ma więc powodu, aby odrzucać hipotezę zerową. Oznacza to, że w analizowanej próbce, ani płeć, ani marka samochodu, ani wiek nie wykazują istotnego statystycznego wpływu na wysokość największych odszkodowań.
