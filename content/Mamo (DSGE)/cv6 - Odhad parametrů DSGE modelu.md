## 1. Kalibrace vs. Estimace
* **Kalibrace:** Parametry $\theta$ jsou fixovány na základě teorie, literatury a dlouhodobých průměrů.
* **Estimace:** Parametry jsou odhadnuty z makrodat $Y_T = \{y_1, \dots, y_T\}$.
* **Problém identifikace parametru:** Různé kombinace parametrů mohou generovat téměř identickou dynamiku pozorovaných veličin $\rightarrow$ data nedokážou parametry jednoznačně odlišit.
* Calibration and estimation are complementary. Calibration is useful when reliable external evidence is available
-  Estimated parameters depend on the model structure, selected observables, andsample. They should not be interpreted as universal characteristics of the economy.

př : persistence technologického šoku (p v technologii jež se řídí AR(1) procesem)
$a_t = \rho a_{t-1} + \varepsilon_t,$ → z literatury je možné určit p=0.95 i p=0.80 → oboje generuje jiné výsledky  → lepší je p a parametry obecně přímo odhadnout na ekonomice co zkoumám 

DSGE závisí na vektoru strukturálních parametrů 
→ tyto parametry determinují preference, produkci, technologii, monetár politiku, šíření šoků (npř discount factor či shock persistence jsou v vektoru struktural parametů)
→ kalibrace ho fixuje, estimace ho odhaduje z dat


---

## 2. Bayesovský princip v DSGE
$$p(\theta \mid Y_T) = \frac{p(Y_T \mid \theta) \, p(\theta)}{p(Y_T)} \propto \underbrace{p(Y_T \mid \theta)}_{\text{Věrohodnost (Likelihood)}} \times \underbrace{p(\theta)}_{\text{Prior}}$$

* **Prior $p(\theta)$:** Apriorní přesvědčení o parametrech před spatřením dat.
	* Odráží ekonomická omezení: $\text{Beta} \in (0,1)$ pro persistence a podíly, $\text{Inv-Gamma} > 0$ pro směrodatné odchylky šoků ($\sigma_e$).
* **Likelihood $p(Y_T \mid \theta)$:** 
	* měří jak se PDF pozorovaných čas. řad mění se strukturálními parametry → vyšší likelihood = pozorovaná ts s danými struktrural parametry je pravděpodobnější
	* Věrohodnost modelu pro daná data; v linearizovaných gaussovských modelech to DYNARE vyhodnocuje pomocí [[1.1 & 1.2 Úvod|Kalmanova filtru]] a stavové reprezentace rovnice
* **Posterior $p(\theta \mid Y_T)$:** Aktualizované rozdělení kombinující apriorní informaci s informací z dat.
→ Bayes mi umožňuje kombinovat info z kalibrace (literatury etc) s infem z dat

> [!tip] Stochastická singularita
> Počet pozorovaných veličin nesmí překročit počet nezávislých šoků (minimálně platí: **# šoků $\ge$ # pozorovaných řad**).

---

## 3. Propojení modelu s daty (Measurement)
Data a proměnné modelu musí být ve **shodné transformaci i škále**:
* Model počítá logaritmické odchylky od steady-state: $y_t = \log(Y_t) - \log(Y_{ss})$.
* Je-li v datech stacionární tempo růstu (první diference), [[Stavová a měřící rovnice |měřicí rovnice]] musí být:
  $$dy_t = y_t - y_{t-1} = \log(Y_t) - \log(Y_{t-1})$$

---

## 4. Konfigurace v Dynare

```matlab
varobs dy; % deklarace pozorované veličiny

estimated_params;
  % název, rozdělení, prior mean, prior std
  alpha,   NORMAL_PDF,    0.35, 0.15;
  beta,    BETA_PDF,      0.97, 0.03;
  omega,   UNIFORM_PDF,   0.50, 0.20;
  stderr e,INV_GAMMA_PDF, 0.10, 2;
  rho,     BETA_PDF,      0.50, 0.20;
end;

estimation(
  datafile = DATASET,
  first_obs = 101, nobs = 80,   % vzorek
  mode_compute = 6,             % optimalizátor pro nalezení modu
  mode_check,                   % diagnostika zakřivení kolem modu
  mh_replic = 50000,            % počet MCMC iterací na řetězec
  mh_nblocks = 2,               % počet paralelních řetězců
  mh_jscale = 0.4,              % škála skoku (řídí acceptance rate ~ 25–35 %)
  mh_drop = 0.4,                % burn-in: zahodí prvních 40 % tahů
  bayesian_irf, irf = 40        % výpočet Bayesovských IRF na 40 období
);
```

---

## 5. Vyhodnocení a diagnostika

### A. Mód vs. Průměr a intervaly
* **Posterior Mode:** Maximum sdružené aposterní hustoty ($\hat{\theta}_{\text{mode}} = \arg\max \{\log p(Y \mid \theta) + \log p(\theta)\}$).
* **Posterior Mean:** Těžiště / průměr z MCMC simulace.
* **Rozdíl mezi modem a meanem:** Vzniká, pokud je posterior **asymetrický (zešikmený)** nebo naráží na hranice definičního oboru (např. $\beta \to 1$ nebo $\omega \to 0$).
* **90% HPD interval:** Nejužší interval pokrývající 90 % aposterní pravděpodobnostní hmoty.

### B. MCMC konvergence (Metropolis-Hastings)
1. **Acceptance rate:** Ideálně v rozmezí **20–40 %** (zde ~31–32 %). Příliš nízká = velké skoky; příliš vysoká = mikroskoky a pomalý průzkum.
2. **Brooks-Gelman diagnostika:** Sleduje rozptyly uvnitř řetězců a mezi nimi (grafy *Interval*, *m2*, *m3*).
   * **Kritérium konvergence:** Červená a modrá křivka musí konvergovat k sobě a ustálit se v horizontální poloze.
3. **Inefficiency factors:** Měří sériovou závislost (autokorelaci) v řetězci. Vysoké hodnoty snižují efektivní počet nezávislých tahů.

### C. Porovnání Prior vs. Posterior
Grafy hustot (šedá = Prior, černá = Posterior, zelená čárkovaná = Mód):
* **Posun středu a zúžení rozptylu:** Data přinesla silnou informaci a výrazně snížila nejistotu (např. $\rho$, $\sigma_e$).
* **Silný překryv (posterior kopíruje prior):** Data obsahují o daném parametru minimum informace; posterior je tažen pouze priorem.

> [!warning] Pozor na vizuální klam
> Vizuální zúžení posterioru **není formálním důkazem identifikace**. Parametr může být zdánlivě „identifikován“ jen vlivem restriktivního prioru.

### D. Bayesovské IRF
* Zobrazují mediánovou/průměrnou odezvu spolu s **nejistotou odhadu parametrů** (pásy spolehlivosti HPD).
* **Level vs. Diference:** Po pozitivním šoku může úroveň výstupu ($y$) zůstat trvale nad původním stavem, zatímco tempo růstu ($dy_t = y_t - y_{t-1}$) se rychle vrátí k nule.

---

## Rychlé shrnutí (Cheat Sheet)

| Krok                      | Nástroj / Metrika        | Co sledovat?                                                                                             |
| :------------------------ | :----------------------- | :------------------------------------------------------------------------------------------------------- |
| **Optimalizace modu**     | `mode_check` grafy       | Hledáme jasný vrchol (kopec). Ploché křivky = slabá identifikace; hraniční řešení = problém specifikace. |
| **MCMC vzorkování**       | `mh_jscale`, akceptace   | Akceptační poměr ~ 25–35 %.                                                                              |
| **Konvergence**           | Brooks-Gelman            | Červená a modrá linie splývají a netrendují.                                                             |
| **Informační přínos dat** | Prior vs. Posterior graf | Zúžení a posun posterioru vůči prioru.                                                                   |
| **Dopad šoků**            | Bayesian IRF             | Znaménko, vrchol, perzistence a šířka HPD pásů.                                                          |
__________________________________________________________________
----------------------------------


---

# Bayesian Estimation of DSGE Models

Tags: #macroeconomics #dsge #dynare #bayesian #econometrics

## 1. Kalibrace vs. Odhad (Motivace)
* **Kalibrace**: Fixní parametry $\theta$ převzaté z literatury/teorie. *Problém:* Různé hodnoty (např. persistence šoku $\rho = 0.95$ vs. $0.80$) mění dynamiku, ale teorie nerozhodne, která lépe odpovídá realitě.
* **Bayesovský odhad**: Kombinuje data (věrohodnost) s expertní informací/teorií (apriorní rozdělení).
* **Identifikace**: Odhad nezaručuje identifikovatelnost – různé kombinace parametrů mohou generovat stejnou dynamiku pozorovaných veličin.

---

## 2. Bayesovský rámec v DSGE
$$p(\theta \mid Y^T) \propto p(Y^T \mid \theta) \, p(\theta)$$
* **Věrohodnost $p(Y^T \mid \theta)$**: U lineárních DSGE modelů s Gaussovskými šoky se vyhodnocuje pomocí **Kalmanova filtru** ze stavové reprezentace.
* **Apriorní rozdělení $p(\theta)$**: Volí se dle ekonomických omezení:
  * `BETA_PDF`: interval $(0, 1)$ – např. diskontní faktor $\beta$, persistence $\rho$, míra amortizace $\delta$.
  * `INV_GAMMA_PDF`: pouze kladné hodnoty – směrodatné odchylky šoků $\sigma_e$.
  * `NORMAL_PDF` / `UNIFORM_PDF`: např. podíly $\alpha$, váhy domácností $\omega$.
* **Posterior Mode vs. Mean**:
  * **Modus**: $\arg\max [\log p(Y^T \mid \theta) + \log p(\theta)]$ (optimalizační bod před MCMC).
  * **Střední hodnota (Mean)**: Průměr z MCMC vzorků.
  * *Rozdíl:* Pokud je posterior sešikmený nebo naráží na hranici podpory (např. $\beta \to 1$), modus a průměr se budou výrazně lišit.
* **90% HPD Interval**: Nejužší interval obsahující 90 % posteriorní pravděpodobnosti.

---

## 3. Data a Měřicí rovnice (Measurement Equation)

> [!warning] Kritický krok: Konzistence jednotek
> Model počítá odchylky od steady-state ($y_t = \log Y_t - \log Y_{ss}$), ale data jsou často v diferencích (růst).

* **Měřicí rovnice**:
  $$dy_t = y_t - y_{t-1} = \Delta \log Y_t$$
* **Stochastická singularita**: Pokud je počet pozorovaných veličin větší než počet strukturních šoků, model zkolabuje (kovarianční matice je singulární). V příkladu: 1 šok ($e_t$) $\to$ 1 pozorovaná veličina ($dy$).

---

## 4. Implementace v Dynare (.mod soubor)

```matlab
// 1. Deklarace pozorované proměnné
varobs dy;

// 2. Apriorní rozdělení (jméno, distribuce, prior mean, prior s.d.)
estimated_params;
    alpha, NORMAL_PDF,    0.35, 0.15;
    beta,  BETA_PDF,      0.97, 0.03;
    delta, BETA_PDF,      0.06, 0.10;
    omega, UNIFORM_PDF,   0.50, 0.20;
    rho,   BETA_PDF,      0.50, 0.20;
    stderr e, INV_GAMMA_PDF, 0.10, 2;
end;

// 3. Konfigurace odhadu
estimation(
    datafile = DATASET,      // Soubor s daty (.mat nebo .m)
    first_obs = 101, nobs = 80, // Výběr vzorku (např. 20 let kvartálních dat)
    mode_compute = 6,        // Monte Carlo optimalizátor modu
    mode_check,              // Vykreslí profily kolem modu
    mh_replic = 50000,       // Počet MCMC iterací na řetězec
    mh_nblocks = 2,          // Počet nezávislých řetězců
    mh_jscale = 0.4,         // Měřítko skoků (ovlivňuje acceptance rate)
    mh_drop = 0.4,           // Burn-in (odstraní prvních 40 % tahů)
    bayesian_irf, irf = 40   // Bayesovské IRF na 40 období
);
```

---

## 5. Vyhodnocení a diagnostika výsledků

1. **Sedí transformace dat?** (Např. demeaning, kvartální růst $\times 100$).
2. **Skončila optimalizace modu v regulérním bodě?** (Kontrola `mode_check`, zda parametry nesedí na hranicích).
3. **Konvergovaly řetězce?** (Brooks-Gelman stabilní, acceptance rate ~30 %).
4. **Co říkají data vs. prior?** (Které parametry se pohly a které jen kopírují prior).
5. **Dávají IRF ekonomický smysl?** (Znaménka, persistence, návrat ke steady-state).
### A. Posterior Mode & `mode_check`
* **Blanchard-Kahn condition**: Řešitelnost a stabilita systému (ověřuje se jako první; neznamená však identifikovatelnost).
* **`mode_check` grafy**: Zobrazují řez log-posterioru při změně jednoho parametru (ostatní fixní).
  * *Ideál:* Hladký parabolický kopec s vrcholem v nalezeném modu.
  * *Varování:* Plochý profil (neidentifikovatelnost), skokové/zubaté funkce, vrchol na hranici intervalu (např. $\beta = 1.0$).

### B. MCMC Konvergence
* **Acceptance rate**: Cílit zhruba na **25–35 %** (reguluje se přes `mh_jscale`).
* **Inefficiency factors**: Měří autokorelaci v řetězci. Vysoké hodnoty = efektivní velikost vzorku je mnohem menší než počet iterací.
* **Brooks-Gelman diagnostika** (Univariate & Multivariate):
  * Porovnává variabilitu uvnitř řetězců (*within-chain*) a mezi řetězci (*between-chain*).
  * **Červená a modrá křivka** se musí v čase **přiblížit k sobě a vodorovně stabilizovat**. Přetrvávající drift = nutno běžet delší řetězec.

### C. Porovnání Prior vs. Posterior
* **Posun polohy + zúžení rozptylu**: Data nesou silnou informaci (posterior se „vzdálil“ od prioru a zpřesnil).
* **Překryv (Posterior $\approx$ Prior)**: Data neobsahují dostatek informace o daném parametru (slabá identifikace, posterior je tažen apriorní volbou).
* *Poznámka:* Samotný vizuální překryv není formální test neidentifikovatelnosti, ale silné varování.

### D. Bayesovské IRF (Impulse Response Functions)
* Zobrazují střední odezvy a **konvoluci nejistoty parametrů i šoků** (kolem mediánu jsou HPD intervaly).
* **Hladina vs. Změna**: Pozor na interpretaci! Růst ($dy$) se může vrátit k nule, zatímco úroveň výstupu ($y$) zůstává trvale vychýlená nad steady-state.



