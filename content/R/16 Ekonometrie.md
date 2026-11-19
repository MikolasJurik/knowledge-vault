
> [!abstract] Základní workflow
> **Specifikace modelu (`formula`) → odhad (`lm()` / `feols()`) → kontrola výsledků (`summary()`, `broom`) → diagnostika → robustní inference → predikce / prezentace výsledků.**

## 1. Formule (`formula`)

Zápis modelu ve tvaru `y ~ x1 + x2`:
- **LHS** (vlevo): vysvětlovaná proměnná $y$.
- **RHS** (vpravo): vysvětlující proměnné / regresory.
- Defaultně je automaticky na pozadí zahrnuta úrovňová konstanta.

```r
model <- Weight ~ Height + Age
lm(model, data = oly12)
```

| Zápis | Význam |
|---|---|
| `y ~ x1 + x2` | Hlavní efekty `x1`, `x2` + konstanta |
| `y ~ x1 - 1` nebo `y ~ x1 + 0` | Model bez konstanty |
| `y ~ log(x)` | Transformace regresoru přímo ve formuli |
| `y ~ I(x^2)` | Druhá mocnina `x`; `I()` vynutí aritmetické vyhodnocení |
| `y ~ I(x1 + x2)` | Součet proměnných jako jeden regresor |
| `y ~ x1:x2` | Pouze interakční člen `x1 × x2` |
| `y ~ x1*x2` | `x1 + x2 + x1:x2` (hlavní efekty i interakce) |
| `y ~ (x1 + x2 + x3)^2` | Hlavní efekty a všechny dvojné interakce; bez trojné interakce |
| `y ~ x >= 1.8` | Logická/dummy proměnná (TRUE/FALSE) |

**Interakce s kategoriální proměnnou:**
```r
Weight ~ Height + Height:Sex   # Různé sklony podle pohlaví, společný intercept
```
$Weight_i = \beta_0 + \beta_1 Height_i + \beta_2(Height_i \cdot Sex_i) + \varepsilon_i$
→ bo npř muž je sex = 0 a žena sex = 1 → 
**1. Pro muže (Sex = 0)** → Po dosazení: $Weight_i = \beta_0 + \beta_1 Height_i + \beta_2(Height_i \cdot 0)$ → $Weight_i = \beta_0 + \beta_1 Height_i$ → sklon je B_1

**2. Pro ženy (Sex = 1)** → Po dosazení: $Weight_i = \beta_0 + \beta_1 Height_i + \beta_2 Height_i$ → $Weight_i = \beta_0 + (\beta_1+\beta_2)Height_i$ → Sklon je  (B_1+B_2)

```r
Weight ~ Height * Sex         # Různé sklony i intercepty podle pohlaví
```
$Weight_i = \beta_0 + \beta_1 Height_i + \beta_2 Sex_i + \beta_3(Height_i \cdot Sex_i) + \varepsilon_i$
> [!warning] Pozor na `Height:Sex` samotné
> Samotný interakční člen nezahrnuje automaticky hlavní efekt `Height`. Pro obvyklý model se společným interceptem a skupinově odlišnými sklony použij `Height + Height:Sex`. Pro odlišné intercepty i sklony použij `Height * Sex`.

### Úprava formule pomocí `update()`

Tečka `.` znamená „dosavadní část formule“.

```r
model <- Weight ~ Height + Age
update(model, . ~ . - Age)  # odebere Age
update(model, . ~ Age)      # RHS nahradí pouze Age
update(model, . ~ . + Sex)  # přidá Sex
```

## 2. Odhad lineárního modelu: `lm()`

```r
est_model <- lm(
  formula = Weight ~ Height + Age,
  data = oly12,
  subset = Age >= 18,
  weights = NULL
)
```

- `formula`: specifikace modelu.
- `data`: datová tabulka; názvy sloupců musí odpovídat formuli.
- `subset`: výběr pozorování použitých k odhadu.
- `weights`: váhy pozorování; při zadání se odhaduje WLS, bez nich OLS.
- `na.action`: způsob zacházení s chybějícími hodnotami (typicky se neúplné řádky vynechají).

### Kategoriální proměnné a `model.matrix()`

Kategoriální proměnná (`factor`) se převede na dummy proměnné. Jedna úroveň je **referenční** a její dummy se vynechá, aby nevznikla perfektní multikolinearita s konstantou (dummy-variable trap).

```r
oly12$Sex <- relevel(factor(oly12$Sex), ref = "F")
model <- Weight ~ Height + Age + I(Age^2) + Sex
X <- model.matrix(model, data = oly12)
```

- `model.matrix()` vytvoří matici regresorů (design matrix), včetně transformací a dummy proměnných.
- Referenční úroveň je zahrnuta v interceptu; koeficient ostatních úrovní vyjadřuje rozdíl vůči referenční úrovni.
- Znakové (`character`) proměnné se při modelování zpravidla převedou na faktory; explicitní `factor()` / `relevel()` umožní řídit pořadí úrovní a referenci.
- Faktor lze vytvořit i přímo ve formuli: `Weight ~ Height + factor(Age)`.

## 3. `fixest::feols()` a fixní efekty

`feols()` je alternativa lm() pro lineární modely, zvlášť při velkém počtu fixních efektů a panelových datech.

```r
library(fixest)

m_fe <- feols(
  Weight ~ Height + Age | Sex + Sport,
  data = oly12,
  vcov = ~ Sport
)
```

**Syntaxe `y ~ x | FE1 + FE2`:**
- Před `|`: běžné regresory a konstanta.
- Za `|`: fixní efekty (zde `Sex` a `Sport`).
- Fixní efekty se efektivně absorbují / odstraňují (demeaning); koeficienty jednotlivých úrovní FE se standardně nevypisují.
- Chceš-li odhadnout konkrétní koeficienty kategorií, vlož proměnnou také do hlavní části, např. `y ~ x + i(a) | b` nebo `y ~ x + factor(a) | b`.

**Fixní efekty** zachycují specifické, nepozorované charakteristiky jednotlivých skupin či jednotek, které mohou ovlivňovat závislou proměnnou. Například chceme vysvětlit mzdu zaměstnanců a máme data z více měst.
$Wage_{it} = \beta_0 + \beta_1 Education_{it} + \alpha_i + \varepsilon_{it}$
Kde:
- $Education_{it}$ – regresor (vzdělání).
- $\alpha_i$ – fixní efekt města.
- $\varepsilon_{it}$ – náhodná chyba.
Každé město má vlastní fixní efekt, který zachycuje jeho specifické charakteristiky (např. místní životní náklady, infrastrukturu nebo dlouhodobé ekonomické podmínky).

### Speciální funkce `fixest`

| Funkce              | Význam                                                                  |
| ------------------- | ----------------------------------------------------------------------- |
| `c(y1, y2)`         | Odhadne stejnou specifikaci modulu zvlášť pro více závislých proměnných |
| `sw(x1, x2)`        | Samostatné specifikace: jednou `x1`, podruhé `x2`                       |
| `sw0(x1, x2)`       | Jako `sw()`, navíc i model bez těchto proměnných                        |
| `csw(x1, x2)`       | Kumulativně: nejprve `x1`, potom `x1 + x2`                              |
| `csw0(x1, x2)`      | Kumulativně, navíc také model bez nich                                  |
| `mvsw(x1, x2, ...)` | Všechny kombinace uvedených proměnných                                  |

npř: `y ~ x0 + csw(x1, x2)` vytvoří modely `y ~ x0 + x1` a `y ~ x0 + x1 + x2`.
nebo:
feols(c(wage, income) ~ education + age, data = df) → odhadne 2 modely : 
1. `wage ~ education + age`
2. `income ~ education + age`
```r
feols(
  y ~ x1 + csw(x2, x3),
  data = df
)
```
### Nastavení standardních chyb v `feols()`

- `vcov = "iid"`: klasické IID standardní chyby.
- `vcov = "hetero"`: heteroskedasticity-robustní standardní chyby.
- `vcov = ~ Sport`: klastrování podle `Sport`.
- `vcov = ~ Sport + Sex`: vícerozměrné klastrování.
- `panel.id = ~ id + year`: identifikace průřezové a časové dimenze panelu pro panelové typy kovariančních matic.
npř :
```r
feols(log(wage) ~ csw(relevel(gender,ref="male"),
                        experience,
                        relevel(union,ref="no"))| ethnicity + education,
             data=wage,vcov="hetero")

```
> Fixní efekty a klastrování nejsou totéž: **fixní efekty** kontrolují nepozorovanou heterogenitu mezi skupinami; **klastrované SE** dovolují korelaci chyb uvnitř skupiny.

Fixní efekty ethnicity a education zde slouží jako kontrolní proměnné. Chceme odhadnout vztah genderu, zkušeností a odborového členství ke mzdě při zohlednění rozdílů ve vzdělání a etnické příslušnosti.

## 4. Odhad více modelů pomocí `purrr`

### Různé specifikace na stejném vzorku

```r
Models <- list(
  baseline = model,
  interaction = update(model, . ~ . + Height:Sex),
  country_FE = update(model, . ~ . + factor(Country))
)

Spec_models <- purrr::map(Models, lm, data = oly12)
summary(Spec_models[[2]])
```

### Stejná specifikace na různých podvzorcích

Např. jeden model pro každý sport:

```r
Sport_models <- oly12 %>%
  group_by(Sport) %>%
  group_split() %>%
  purrr::map(~ lm(model, data = .x))
```

- `map()` aplikuje funkci na každý prvek listu a vrací list výsledků.
- `group_split()` rozdělí tabulku na list dílčích tabulek podle skupin.

## 5. Výsledky modelu a balík `broom`

### Užitečné funkce pro objekt `lm`

| Funkce | Co vrací |
|---|---|
| `summary(model)` | Souhrn odhadu, testy koeficientů a fit modelu |
| `coef(model)` / `coefficients(model)` | Odhadnuté koeficienty |
| `residuals(model)` / `resid(model)` | Rezidua $\hat{u}_i$ |
| `fitted(model)` | Vyrovnané hodnoty $\hat{y}_i$ |
| `predict(model, newdata)` | Predikce pro nová data |
| `confint(model)` | Intervaly spolehlivosti koeficientů |
| `vcov(model)` | Odhad kovarianční matice koeficientů |
| `nobs(model)` | Počet použitých pozorování |
| `AIC(model)`, `BIC(model)` | Informační kritéria pro porovnání modelů |
| `logLik(model)` | Logaritmus věrohodnosti |
| `deviance(model)` | U `lm`: reziduální součet čtverců (RSS), nikoli MSE |

Základní komponenty objektu: `model$coefficients`, `model$residuals`, `model$fitted.values`, `model$model` (data skutečně použitá při odhadu).

### Převod pomocí `broom`

```r
library(broom)

tidy(est_model)    # tabulka koeficientů, SE, testových statistik a p-hodnot
glance(est_model)  # souhrnné statistiky modelu jako celku
augment(est_model) # původní pozorování + fitted, rezidua a diagnostické údaje
```

`augment()` vrátí původní data doplněná o sloupce (predikce, rezidua, leverage ... - pro každý datapoint) →  vhodný pro grafy (zde vizualizuju predikci modelu a real hodnotu):

```r
augment(est_model) %>%
  ggplot(aes(x = Weight, y = .fitted)) +
  geom_point(alpha = 0.15) +
  geom_abline(slope = 1, intercept = 0)
```

Ideální predikce leží na diagonále $\hat y = y$.

## 6. Regresní tabulky: `modelsummary` a `gt`

`modelsummary()` umí zpracovat jeden model, seznam modelů i pojmenovaný seznam.

```r
library(modelsummary)

modelsummary(
  Spec_models,
  fmt = 3,
  estimate = "{estimate}{stars}",
  statistic = "std.error",
  stars = c("*" = .10, "**" = .05, "***" = .01),
  gof_map = c("nobs", "adj.r.squared")
)
```

Důležité argumenty:
- `output`: formát nebo objekt výstupu (`"html"`, `"docx"`, `"latex"`, `"gt"`, `"data.frame"` aj.).
- `fmt`: počet desetinných míst / formát čísel.
- `estimate`: formát odhadů, např. `"{estimate}{stars}"`.
- `statistic`: statistika pod koeficientem, např. `"std.error"`.
- `stars`: hranice pro hvězdičky statistické významnosti.
- `coef_map`: přejmenování a výběr koeficientů (zároveň určuje jejich pořadí).
- `gof_map`: výběr a pořadí souhrnných statistik (např. počet pozorování, adjustované $R^2$).

Další úpravy tabulky přes `gt`:

```r
m_gt <- modelsummary(Spec_models, output = "gt")

m_gt %>%
  tab_header(title = "Výsledky regrese") %>%
  cols_label(model1 = "(1)", model2 = "(2)")
```

## 7. Predikce a out-of-sample validace

Rozděl data na trénovací a testovací část. Model odhadni pouze na trénovacích datech a vyhodnoť ho na datech, která při odhadu neviděl.

```r
set.seed(123)
idx <- sample(nrow(oly12), round(nrow(oly12) / 2))
train <- oly12[idx, ]
test  <- oly12[-idx, ]

m_train <- lm(model, data = train)
pred <- predict(m_train, newdata = test)

RMSE <- sqrt(mean((test$Weight - pred)^2, na.rm = TRUE))
MAE  <- mean(abs(test$Weight - pred), na.rm = TRUE)
```

- **In-sample**: predikce na datech použitých k odhadu; obvykle optimističtější.
- **Out-of-sample**: predikce na nových / odložených datech; lépe měří predikční schopnost.
- RMSE více penalizuje velké chyby; MAE je průměrná absolutní chyba.
- `set.seed()` zajišťuje reprodukovatelné náhodné rozdělení.
- Při časových řadách se data obvykle nerozdělují náhodně; trénink musí časově předcházet testu.

## 8. Diagnostika modelu

### 8.1 Vlivná pozorování a leverage

- **Leverage**: neobvyklá poloha pozorování v prostoru regresorů; měří ji `hatvalues()`.
- **DFBETAS**: vliv vynechání pozorování na jednotlivé odhadnuté koeficienty.
- **DFFITS**: vliv pozorování na jeho fitted hodnotu.
- **Cook's distance**: souhrnný vliv pozorování na celý odhad.
- `influence.measures(model)` vrací diagnostické míry a příznaky potenciálně vlivných pozorování (`$infmat`, `$is.inf`).

```r
infl <- influence.measures(est_model)
infl$infmat
infl$is.inf
cooks.distance(est_model)
hatvalues(est_model)
```


### 8.2 Normalita reziduí

Graficky – Q-Q plot:

```r
augment(est_model) %>%
  ggplot(aes(sample = .std.resid)) +
  geom_qq() +
  geom_qq_line()
```

- Body přibližně na přímce → rezidua jsou přibližně normální.
- Systematické odchylky, zejména v koncích → asymetrie / těžké či lehké chvosty.
- Normalita není podmínkou nevychýlenosti OLS; je důležitá hlavně pro přesnou klasickou inferenci v malých vzorcích.
- KS test na reziduích s odhadnutými parametry normálního rozdělení nemá běžné tabulkové rozdělení testové statistiky; Q-Q plot bývá užitečnější. Případné testy normality interpretuj opatrně.

### 8.3 Linearita – Rainbow test

`lmtest::raintest()` testuje, zda je lineární specifikace adekvátní.

```r
lmtest::raintest(model, order.by = ~ Height, data = oly12)
```

- $H_0$: lineární specifikace je adekvátní.
- Malá p-hodnota → zamítáme $H_0$; výsledek indikuje možnou nelinearitu / chybnou specifikaci.
- `fraction` určuje podíl prostředních pozorování použitých pro dílčí odhad; `order.by` určuje proměnnou, podle níž se pozorování řadí.

### 8.4 Heteroskedasticita

- **Homoskedasticita**: $Var(u_i \mid X) = \sigma^2$.
- **Heteroskedasticita**: $Var(u_i \mid X)$ se mění podle pozorování / regresorů.
- Při exogenitě zůstávají OLS koeficienty nevychýlené (a za standardních podmínek konzistentní), ale klasické OLS směrodatné chyby a testy mohou být nesprávné; OLS již není BLUE.

Testy:

```r
lmtest::bptest(est_model) # Breusch–Pagan test
car::ncvTest(est_model)   # test nekonstantního rozptylu
```

U obou testů je obvykle $H_0$: homoskedasticita. Malá p-hodnota → evidence heteroskedasticity.

### 8.5 Robustní a klastrované směrodatné chyby

Robustní SE **nemění OLS koeficienty**, ale upravují odhad jejich kovarianční matice, a tím směrodatné chyby, testy a intervaly spolehlivosti.

```r
library(sandwich)
library(lmtest)

# Heteroskedasticity-consistent kovarianční matice
V_HC <- vcovHC(est_model, type = "HC3")
coeftest(est_model, vcov. = V_HC)

# Klastrování podle Sport
V_CL <- vcovCL(est_model, cluster = oly12$Sport)
coeftest(est_model, vcov. = V_CL)

# Dvě dimenze klastrování
V_2way <- vcovCL(est_model, cluster = list(oly12$Sport, oly12$Sex))
coeftest(est_model, vcov. = V_2way)
```

- `vcovHC()`: robustní kovarianční matice při heteroskedasticitě; běžné volby jsou HC0–HC5 (často HC3).
- `vcovCL()`: klastrovaná kovarianční matice; používá se, když mohou být chyby korelované uvnitř skupin (např. sportu, firmy, obce).
- Klastrování musí odpovídat struktuře závislosti chyb; důležitý je také dostatečný počet clusterů.
- `coeftest()` provede testy koeficientů s dodanou kovarianční maticí.

### 8.6 Diagnostické grafy

```r
library(ggfortify)
autoplot(est_model)
```

Čtyři standardní grafy:
1. **Residuals vs Fitted** – nelinearita (zakřivený vzor), heteroskedasticita (trychtýř).
2. **Normal Q-Q** – odchylky od normality reziduí.
3. **Scale-Location** – konstantnost rozptylu reziduí.
4. **Residuals vs Leverage** – pozorování s vysokou pákou a vlivná pozorování (Cook's distance).

## 9. Rychlé zapamatování – co si neplést

| Pojmy | Rozdíl |
|---|---|
| `:` vs. `*` | `:` = interakce; `*` = oba hlavní efekty + interakce |
| `factor()` vs. `relevel()` | `factor()` vytvoří faktor; `relevel()` nastaví referenční úroveň |
| `model.matrix()` vs. `lm()` | První vytvoří matici regresorů; druhý odhadne model |
| Fixní efekty vs. cluster SE | FE kontrolují skupinovou heterogenitu; cluster SE upravují inferenci při závislosti chyb uvnitř skupin |
| `tidy()` / `glance()` / `augment()` | Koeficienty / souhrn modelu / pozorování s diagnostickými hodnotami |
| Klasické vs. robustní SE | Koeficienty stejné; liší se kovarianční matice, SE a testy |
| In-sample vs. out-of-sample | Tréninková data vs. data nepoužitá k odhadu |
| RSS vs. MSE | `deviance(lm)` = RSS; MSE je průměrná čtvercová chyba (záleží na použité definici jmenovatele) |

---

## 10. Minimální pracovní šablona

```r
library(tidyverse)
library(broom)
library(lmtest)
library(sandwich)

# 1) Specifikace a odhad
model <- Weight ~ Height * Sex + Age + I(Age^2)
est_model <- lm(model, data = oly12)

# 2) Výsledky
summary(est_model)
tidy(est_model)
glance(est_model)

# 3) Robustní inference
coeftest(est_model, vcov. = vcovHC(est_model, type = "HC3"))

# 4) Diagnostika
autoplot(est_model)
bptest(est_model)
influence.measures(est_model)

# 5) Predikce
predict(est_model, newdata = oly12)
```


