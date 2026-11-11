
### 1. Co je to vlastně časová řada?

V klasické statistice máme výběr nezávislých pozorování (např. změříme výšku 100 náhodných lidí na ulici). V časových řadách je to jinak:
* **Časová řada** je posloupnost dat seřazených v čase: $y_1, y_2, \dots, y_T$.
* **Klíčový rozdíl:** Pozorování **nejsou nezávislá**. To, co se stane dnes ($y_t$), velmi silně závisí na tom, co se stalo včera ($y_{t-1}$).
* **Stochastický proces:** Představ si ho jako „stroj na generování reality“. Historie, kterou vidíme v datech, je jen **jedna konkrétní trajektorie** (realizace) z mnoha možných vesmírů, které ten proces mohl vytvořit.


### 2. Bílý šum (White Noise) = náhodná složka = $\varepsilon_t$ , $e_t$)

Než postavíme jakýkoliv model, potřebujeme čistou, ničím neovlivněnou náhodu. Tomu říkáme **bílý šum** (značíme obvykle .

Představ si ho jako házení férovou mincí nebo šum v rádiu:
1. **Průměr je nula:** $E[\varepsilon_t] = 0$ (šoky se v průměru vyruší).
2. **Rozptyl je stálý:** $Var(\varepsilon_t) = \sigma^2$ (šum není včera divočejší než zítra).
3. **Žádná paměť:** $Cov(\varepsilon_t, \varepsilon_s) = 0$ pro $t \neq s$ (dnešní šok ti neřekne vůbec nic o tom, jaký šok přijde zítra).

### 3. Stacionarita

#### A. Striktní stacionarita
Celé rozdělení pravděpodobnosti se s časem vůbec nemění. Ať se podíváš na data v roce 1950 nebo 2026, pravděpodobnostní hustota je identická. To je v praxi příliš přísné a těžko ověřitelné.

#### B. Slabá (kovarianční) stacionarita 
V praxi nám stačí sledovat jen první dva momenty (průměr, rozptyl a kovarianci). Proces je slabě stacionární, pokud splňuje **3 podmínky**:

1. **Konstantní střední hodnota:** 
   $$E[y_t] = \mu$$
   *(Data neujíždějí nahoru ani dolů, oscilují kolem stálého středu.)*

2. **Konstantní rozptyl:** 
   $$Var(y_t) = \sigma^2$$
   *(Rozptyl dat neexploduje do nekonečna ani se časem nezmenšuje.)*

3. **Autokovariance závisí jen na odstupu (lagu $k$), ne na konkrétním datu:**
   $$Cov(y_t, y_{t-k}) = \gamma_k$$
   *(Vztah mezi „dnes“ a „před měsícem“ je stejný v lednu jako v srpnu = není sezonalita)*

> [!danger] Co hrozí, když data nejsou stacionární?
> **==🔴Zdánlivá regrese== (Spurious Regression):** Dáš do regrese dvě naprosto nesouvisející nestacionární řady (např. *počet rozvodů v ČR* a *dovoz ropy z Norska* – obojí v čase roste). Výsledkem bude statisticky vysoce významný vztah ($R^2 > 0.9$), který je ale čistý nesmysl.

---

### 4. Jak měříme paměť procesu: ACF a PACF

Abychom poznali, jak se proces chová, díváme se na závislost mezi $y_t$ a jeho minulostí $y_{t-k}$:

#### 1. Autokorelační funkce (ACF)
* Měří **celkovou korelaci** mezi dneškem $y_t$ a hodnotou před $k$ obdobími $y_{t-k}$.
* *Příklad:* Jak moc teplota dnes souvisí s teplotou předevčírem? Určitě hodně. Ale z velké části proto, že předevčírek ovlivnil včerejšek a včerejšek ovlivnil dnešek.

#### 2. Parciální autokorelační funkce (PACF)
* Měří **čistou (přímou) korelaci** mezi $y_t$ a $y_{t-k}$ poté, co **odfiltruje vliv všech mezidobí** ($y_{t-1}, y_{t-2}, \dots$).
* *Příklad:* O kolik ti znalost předevčírejší teploty pomůže zpřesnit předpověď na dnešek, *pokud už znáš teplotu ze včerejška*? U jednoduchých procesů už téměř vůbec.
AR je pak samotný autoregresivní model → AR != ACF

---

### Kvíz

Zkus vlastními slovy odpovědět na tyto 3 rychlé otázky:

1. **Proč je bílý šum $\varepsilon_t$ stacionární?** (Zkus se podívat na ty 3 podmínky).
2. Představ si řadu HDP, která každý rok stabilně roste o $3\,\%$. **Je taková řada stacionární?** Pokud ne, která podmínka je porušena?
3. Jaký je rozdíl mezi **ACF** a **PACF** na příkladu zpoždění o 2 období ($k = 2$)?

1.Jelikož se v čase nemění jeho střední hodnota, rozptyl ani autokovariace
2.není stacionární - střední hodnota se zvětšuje
3.ACF zachycuje celkovou autokorelaci mezi t a t-2, PACF tuto autokorelaci očišťuje o autorkorelaci t-1 s t → ukáže čistě korelaci t s t-2