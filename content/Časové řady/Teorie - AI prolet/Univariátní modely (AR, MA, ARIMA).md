
### 1. Operátor zpoždění (Lag Operator $L$)
Zkracuje zápis zpožděných veličin:
* $L y_t = y_{t-1}$
* $L^k y_t = y_{t-k}$
* Diference: $\Delta y_t = y_t - y_{t-1} = (1 - L)y_t$

---

### 2. Autoregresní model – AR($p$)
Hodnota $y_t$ závisí na svých minulých hodnotách a novém šoku $\varepsilon_t \sim WN(0, \sigma^2)$.

#### Model AR(1):
$$y_t = c + \phi_1 y_{t-1} + \varepsilon_t$$

* **Podmínka stacionarity:** $|\phi_1| < 1$.
  * Pokud $|\phi_1| < 1$, proces se po šoku vždy vrátí ke střední hodnotě $\mu = \frac{c}{1 - \phi_1}$.
  * Pokud $\phi_1 > 1$, proces exploduje do nekonečna.
* **Projevy v datech:**
  * **ACF:** Doznívá geometricky/exponenciálně k nule (dlouhá paměť).
  * **PACF:** Usekne se přesně po $p$ tazích (u AR(1) je významný jen lag 1, ostatní jsou nulové).

---

### 3. Jednotkový kořen a Náhodná procházka (Random Walk)
Co se stane, když je v AR(1) přesně $\phi_1 = 1$?

$$y_t = y_{t-1} + \varepsilon_t = y_0 + \sum_{i=1}^t \varepsilon_i$$

* **Vlastnosti:**
  * Střední hodnota je konstantní ($y_0$).
  * **Rozptyl roste s časem:** $Var(y_t) = t \cdot \sigma^2 \to \infty$.
  * **Paměť je nekonečná:** Šok $\varepsilon$ z času $t=1$ zůstává v systému navždy.
* **Závěr:** Proces má **jednotkový kořen** (*unit root*) a je **nestacionární**.

---

### 4. Model klouzavých průměrů – MA($q$)
Hodnota $y_t$ nezávisí na minulých $y$, ale na **minulých šocích** $\varepsilon$.

#### Model MA(1):
$$y_t = \mu + \varepsilon_t + \theta_1 \varepsilon_{t-1}$$

* **Stacionarita:** MA proces je **vždy stacionární** pro libovolné konečné $\theta$.
* **Invertibilita:** Pokud $|\theta_1| < 1$, lze MA proces přepsat jako nekonečný AR proces (umožňuje odhadnout minulé šoky z pozorovaných dat).
* **Projevy v datech:**
  * **ACF:** Usekne se po $q$ tazích (u MA(1) je nenulový jen lag 1, pak nula).
  * **PACF:** Doznívá geometricky k nule.

---

### Shrnutí: Jak podle grafů poznat AR vs. MA

| Model | ACF | PACF |
| :--- | :--- | :--- |
| **AR($p$)** | Doznívá postupně | **Usekne se** po $p$ zpožděních |
| **MA($q$)** | **Usekne se** po $q$ zpožděních | Doznívá postupně |
| **ARMA($p,q$)** | Doznívá postupně | Doznívá postupně |

---

### 5. ARIMA($p, d, q$) – Léčba nestacionarity
Pokud řada není stacionární kvůli jednotkovému kořenu, musíme ji **diferencovat**:
* **$p$**: řád autoregrese (AR)
* **$d$**: počet diferencování nutných pro dosažení stacionarity ($I(d)$)
  * Původní řada: $y_t \sim I(1)$ (nestacionární, např. log HDP)
  * První diference: $\Delta y_t = y_t - y_{t-1} \sim I(0)$ (stacionární, např. růst HDP)
* **$q$**: řád klouzavých průměrů (MA)

Matematický zápis:
$$\phi(L)(1 - L)^d y_t = c + \theta(L)\varepsilon_t$$

---

### 6. Testování jednotkového kořene: ADF test
**Augmented Dickey-Fuller (ADF)** test ověřuje přítomnost jednotkového kořene.

* **$H_0$:** Proces má jednotkový kořen ($\phi = 1$, je nestacionární).
* **$H_1$:** Proces je stacionární ($|\phi| < 1$).
* **Pravidlo rozhodování:** Pokud je p-hodnota $< 0.05$, zamítáme $H_0$ $\to$ řada je stacionární.

---

### Kontrolní kvíz

1. Máš model $y_t = 0.4 y_{t-1} + \varepsilon_t$. O jaký model se jedná, je stacionární a jak bude vypadat jeho PACF graf?
2. Proč je náhodná procházka ($y_t = y_{t-1} + \varepsilon_t$) nestacionární, i když její střední hodnota se v čase nemění?
3. Časová řada cen akcií je $I(1)$. Co musíš s daty udělat, abys získal stacionární řadu $I(0)$ vhodnou pro ARMA modelování?