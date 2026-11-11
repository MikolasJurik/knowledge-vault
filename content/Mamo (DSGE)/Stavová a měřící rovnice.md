Tags: #econometrics #dsge #state-space #kalman-filter

> [!abstract] Podstata v jedné větě
> **Měřicí rovnice je most mezi teorií a realitou** – propojuje nepozorované (latentní/modelové) veličiny se skutečnými pozorovanými daty.

---

### 1. Matematický princip

$$y_t = x_t + \varepsilon_t$$

* $y_t$ = **pozorovaná data** (to, co měříme, např. naměřená inflace, růst HDP)
* $x_t$ = **latentní stav** (skutečná nepozorovaná veličina, např. produkční mezera, technologický šok)
* $\varepsilon_t$ = **chyba měření** (measurement error) nebo konstanta/trend

---

### 2. Měřicí vs. Stavová rovnice

| Typ rovnice | Co dělá? | Vztah | Příklad |
| :--- | :--- | :--- | :--- |
| **Stavová** *(State eq.)* | Popisuje **vývoj ekonomiky v čase** (jak funguje vnitřek modelu) | $x_t$ v závislosti na $x_{t-1}$ | $x_t = \rho x_{t-1} + \eta_t$ |
| **Měřicí** *(Measurement eq.)* | Popisuje **projekci modelu do reality** (jak vnitřek vidíme zvenčí) | $y_t$ v závislosti na $x_t$ | $y_t = x_t + \varepsilon_t$ |

---

### 3. Proč je klíčová v DSGE modelech?

V DSGE modelech proměnné existují jako teoretické odchylky od steady-state, které nelze přímo stáhnout z ČSÚ/Eurostatu.

* **Příklad v DSGE:**
  $$GDP_t^{obs} = \gamma + GDP_t^{model}$$
  *(pozorovaný růst = trend $\gamma$ + modelová dynamika)*

* **Řetězec v Bayesovském odhadu:**
  $$\text{DSGE model} \xrightarrow{\text{vnitřní dynamika}} \text{Stavová repz.} \xrightarrow[\text{rovnice}]{\text{\textbf{Měřicí}}} \text{Reálná data} \xrightarrow{\text{Kalman}} \text{Likelihood} \xrightarrow{\text{Prior}} \text{Posterior}$$

> [!tip] Rychlá mnemotechnika
> * **Stavová rovnice** = jak se věci *hýbou uvnitř*.
> * **Měřicí rovnice** = přes jaké brýle se na ně *díváme zvenku*.

Tady je konkrétní příklad, který můžeš rovnou vložit na konec zápisu:

---

### 4. Konkrétní příklad z praxe (Růst HDP a úroková sazba)

Typický problém: **Model počítá v jiných jednotkách než statistický úřad.**

#### Příklad A: Růst HDP (Diference + Převod na %)
* **Co máme v datech ($y_t^{obs}$)**: Kvartální růst HDP v procentech stažený z ČSÚ (např. $0.8\,\%$).
* **Co počítá model ($y_t$)**: Logaritmickou odchylku výstupu od steady-state (číslo kolem nuly, např. $0.005$).
* **Měřicí rovnice**:
  $$dy_t^{obs} = \bar{g} + 100 \cdot (y_t - y_{t-1})$$
  * $\bar{g}$ = tempo růstu y v steady state
  * $(y_t - y_{t-1})$ = modelový růst mezi kvartály
  * $100 \cdot$ = převod z desetinného čísla na procenta
  * **$dy_t$** značí **mezikvartální růst HDP** (výstupu) v čase $t$. = změnu = $\Delta$ 

**$dy_t^{obs}$ (v datech / levá strana rovnice)**: 
   * Skutečně **pozorovaný růst HDP** stažený ze statistického úřadu (např. reálný růst o $+0.8\ \% \text{ q-o-q}$).

**$dy_t = y_t - y_{t-1}$ (v modelu / pravá strana rovnice)**:
   * **Modelový růst**, tedy rozdíl logaritmických odchylek výstupu mezi minulým a současným kvartálem:
     $dy_t = y_t - y_{t-1} \approx \log(Y_t) - \log(Y_{t-1})$



#### Příklad B: Úroková sazba (Roční báze)
* **Co máme v datech ($R_t^{obs}$)**: Roční 3M PRIBOR v procentech (např. $5.25\,\%$ p.a.).
* **Co počítá model ($r_t$)**: Kvartální odchylku úrokové sazby.
* **Měřicí rovnice**:
  $$R_t^{obs} = \bar{R} + 4 \cdot 100 \cdot r_t$$
  * $\bar{R}$ = dlouhodobá rovnovážná úroková sazba = v kanon DSGE je to rovno steady state výnois kapitálu
  * $4 \cdot$ = převod z kvartální modelové sazby na roční p.a. hodnotu
  * $100 \cdot$ = převod na procenta



