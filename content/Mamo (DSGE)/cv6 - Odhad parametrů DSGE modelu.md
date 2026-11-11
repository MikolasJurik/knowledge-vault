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
* Je-li v datech stacionární tempo růstu (první diference), měřicí rovnice musí být:
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