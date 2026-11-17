
## 1. Od odhadu k hodnocení modelu (Estimation vs. Verification vs. Comparison)

Úspěšný Bayesovský odhad parametrů (konvergence MCMC, ekonomicky rozumné hodnoty) **automaticky neznamená, že je model dobrý**. Například model může dobře trefit volatilitu inflace, ale zcela selhat v zachycení její perzistence (šoky v modelu vyprchají příliš rychle).

Proto rozlišujeme **tři odlišné fáze práce s DSGE modelem**:

| Fáze | Hlavní otázka |
| :--- | :--- |
| **Odhad (*Estimation*)** | Které hodnoty parametrů jsou podpořeny pozorovanými daty (podmíněně na dané specifikaci modelu)? |
| **Verifikace (*Verification*)** | Reprodukuje odhadnutý model důležité empirické charakteristiky makroekonomických fluktuací (volatilitu, korelace, perzistenci)? |
| **Porovnání (*Comparison*)** | Která specifikace modelu získává silnější celkovou podporu z dat (pomocí marginální věrohodnosti a Bayesova faktoru)? |

> [!IMPORTANT] Klíčový princip
> Žádná jednotlivá diagnostika neposkytuje kompletní zhodnocení kvality modelu. Empirické momenty a Bayesovské porovnání modelů se vzájemně doplňují.

---

## 2. Třírovnicový Novokeynesiánský (NK) model (`NK3.mod`)

Model popisuje společné určení ekonomické aktivity, inflace a nominální úrokové míry. Všechny proměnné jsou vyjádřeny jako **odchylky od ustáleného stavu (*steady state*)**:
- $x_t$ – mezera výstupu (*output gap*)
- $\pi_t$ – inflace (*inflation*)
- $r_t$ – nominální úroková míra (*nominal interest rate*)

### Základní rovnice modelu
1. **Dynamická IS křivka** (mezičasové rozhodování domácností):
   $$x_t = E_t x_{t+1} - \frac{1}{\varphi}(r_t - E_t \pi_{t+1}) + g_t$$
   - Vyšší očekávaná reálná úroková míra $(r_t - E_t \pi_{t+1})$ zdražuje současnou spotřebu oproti budoucí a snižuje mezeru výstupu.
   - Vyšší $\varphi$ (inverzní elasticita mezičasové substituce) znamená slabší reakci $x_t$ na změnu reálné úrokové míry.
2. **Novokeynesiánská Phillipsova křivka (NKPC)** (cenotvorba firem):
   $$\pi_t = \beta E_t \pi_{t+1} + \kappa x_t + u_t$$
   - Inflace je vpředhledící (*forward-looking*) – závisí na očekávané budoucí inflaci a na tlacích z reálné ekonomiky ($\kappa x_t$).
3. **Pravidlo měnové politiky (Taylorovo pravidlo se vyhlazováním):**
   $$r_t = \psi_r r_{t-1} + (1 - \psi_r)(\psi_x x_t + \psi_p \pi_t) + \varepsilon_{r,t}$$
   - Centrální banka reaguje na mezeru výstupu ($\psi_x$) a inflaci ($\psi_p$), přičemž úrokovou sazbu mění postupně (*interest rate smoothing* $\psi_r$). Vyšší $\psi_r$ vnáší do úrokové míry dodatečnou perzistenci.

**Zpětnovazební mechanismus:** Měnová politika mění reálnou úrokovou sazbu $\implies$ ovlivňuje ekonomickou aktivitu ($x_t$) $\implies$ aktivita ovlivňuje inflaci ($\pi_t$) $\implies$ inflace a aktivita se zpětně promítají do pravidla měnové politiky.

### Strukturální šoky a parametry modelu
Makroekonomické fluktuace pohánějí **3 strukturální šoky**:
- **Poptávkový šok** (posouvá IS křivku): $g_t = \rho_g g_{t-1} + \varepsilon_{g,t}$
- **Nákladový / nabídkový šok** (*cost-push*, posouvá NKPC): $u_t = \rho_u u_{t-1} + \varepsilon_{u,t}$
- **Šok měnové politiky**: $\varepsilon_{r,t}$ (vstupuje přímo do pravidla CB)

Inovace jsou vzájemně nezávislé a normálně rozdělené:
$$\varepsilon_{j,t} \sim N(0, \sigma_j^2), \qquad j \in \{g, u, r\}$$
- **Parametry perzistence ($\rho_g, \rho_u$)** určují, jak rychle odezní dopad šoku.
- **Směrodatné odchylky ($\sigma_j$)** určují typickou velikost nových inovací.
- *Systémový pohled:* Např. perzistence inflace nemusí vznikat jen z $\rho_u$, ale i ze vyhlazování sazeb $\psi_r$, sklonu $\kappa$ nebo jejich interakce $\implies$ model je nutné odhadovat jako systém.

| Parametr | Ekonomická interpretace |
| :---: | :--- |
| $\beta$ | Diskontní faktor |
| $\varphi$ | Inverzní elasticita mezičasové substituce |
| $\kappa$ | Sklon Novokeynesiánské Phillipsovy křivky |
| $\psi_r$ | Vyhlazování úrokové míry (*interest rate smoothing*) |
| $\psi_x$ | Reakce měnové politiky na mezeru výstupu |
| $\psi_p$ | Reakce měnové politiky na inflaci |
| $\rho_g$ | Perzistence poptávkového šoku |
| $\rho_u$ | Perzistence nákladového (*cost-push*) šoku |
| $\sigma_g$ | Směrodatná odchylka poptávkové inovace |
| $\sigma_u$ | Směrodatná odchylka nákladové inovace |
| $\sigma_r$ | Směrodatná odchylka inovace měnové politiky |

### Měřicí rovnice a přechod ke 3 pozorovaným proměnným
Teoretické proměnné jsou odchylky od steady state (např. mezera výstupu $x_t$ není přímo pozorovaná jako růst HDP). Proto definujeme **měřicí rovnice (*measurement equations*)**:
$$y_{\text{obs},t} = x_t - x_{t-1} + xtrend$$
$$p_{\text{obs},t} = \pi_t + constepinf$$
$$R_{\text{obs},t} = r_t + conster$$
- Konstanty `xtrend` (průměrný trendový růst), `constepinf` (průměrná inflace) a `conster` (průměrná úroková míra) jsou **měřicí parametry**, nikoliv strukturální šoky – slaďují teoretický steady state s průměrnou úrovní dat.
- V Dynare deklarováno pomocí: `varobs y_obs p_obs R_obs;`

**Proč používáme 3 pozorované proměnné (`y_obs`, `p_obs`, `R_obs`) místo jedné?**
- **Růst výstupu** informuje o dynamice aktivity; **inflace** o cenotvorbě a nákladových šocích; **nominální sazba** o chování měnové politiky.
- **Společný pohyb (*co-movement*)** pomáhá rozlišit konkurenční zdroje fluktuací (např. poptávkový i nákladový šok oba zvyšují inflaci, ale mají opačný či odlišný dopad na výstup).
- *Pozor:* Více pozorovaných proměnných může zlepšit identifikaci parametrů, ale **nezaručuje** ji.

---

## 3. Bayesovský odhad v Dynare

### Příprava dat a výběr vzorku (`DATA_PREP_AND_RUN.m`)
Skript načte `DATA_USA.csv`, vybere tři čtvrtletní řady USA (`y_obs = data.dy`, `p_obs = data.piobs`, `R_obs = data.robs`) a uloží je do `data_for_estimation.mat`.
- **Odhadovací vzorek:** prvních **85 čtvrtletních pozorování**.
- **Zásada pro porovnání modelů:** Obě specifikace musí být odhadnuty na **témže vzorku** a se stejnou transformací dat (jinak nelze porovnávat posteriorní rozdělení ani marginální věrohodnosti).

### Apriorní rozdělení (`estimated_params` v `NK3.mod`)
Volba priorů reflektuje ekonomická omezení parametrů:
- **Beta (`beta_pdf`)** pro parametry v $(0, 1)$: $\psi_r, \rho_g, \rho_u$
- **Gamma (`gamma_pdf`)** pro kladné parametry: $\varphi, \kappa, constepinf$
- **Normální (`normal_pdf`)** pro neomezené parametry: $\psi_x, \psi_p, xtrend, conster$
- **Inverzní Gamma (`inv_gamma_pdf`)** pro směrodatné odchylky šoků: $\sigma_g, \sigma_u, \sigma_r$

```matlab
estimated_params;
    phi,        gamma_pdf,     2,     0.5;
    kappa,      gamma_pdf,     0.5,   0.3;
    psix,       normal_pdf,    0.5,   0.25;
    psir,       beta_pdf,      0.8,   0.15;
    psip,       normal_pdf,    1.7,   1.95;
    rhog,       beta_pdf,      0.8,   0.1;
    rhou,       beta_pdf,      0.8,   0.1;
    stderr eg,  inv_gamma_pdf, 1,     10;
    stderr eu,  inv_gamma_pdf, 1,     10;
    stderr er,  inv_gamma_pdf, 1,     10;
    xtrend,     normal_pdf,    0.4,   0.1;
    constepinf, gamma_pdf,     0.625, 0.1;
    conster,    normal_pdf,    1.0,   0.1;
end;
```

### Nastavení odhadu a výstupní struktury Dynare
Příkaz `estimation(...)` v `NK3.mod` používá:

| Volba | Význam |
| :--- | :--- |
| `datafile = data_for_estimation` | Načte připravené makroekonomické řady |
| `first_obs = 1, nobs = 85` | Vybere prvních 85 pozorování |
| `mode_compute = 4` | Optimalizační rutina pro nalezení modu posterioru |
| `mh_replic = 100000, mh_nblocks = 2` | Spustí 2 MCMC řetězce po 100 000 iteracích |
| `mh_drop = 0.75` | Zahodí prvních 75 % (*burn-in*), zbývá 25 000 výběrů na řetězec |
| `forecast = 10`, `smoother` | Predikce na 10 období a vyhlazené odhady stavů/šoků |
| `bayesian_irf, irf = 20` | Bayesovské impulsní odezvy na 20 období |

Následuje historická dekompozice šoků a výpočet teoretických momentů:
```matlab
shock_decomposition(parameter_set=posterior_mean) y_obs p_obs R_obs;
stoch_simul y_obs p_obs R_obs;
```

Výsledky jsou uloženy v `NK3/Output/NK3_results.mat` ve třech hlavních strukturách MATLABu:

| Struktura | Obsah |
| :--- | :--- |
| `M_` | Definice modelu, hodnoty parametrů (`M_.params`), názvy a **pořadí** endogenních proměnných (`M_.endo_names`) |
| `oo_` | Numerické výsledky: teoretická kovarianční matice (`oo_.var`), autokorelace (`oo_.autocorr`), IRFs |
| `options_` | Nastavení použitá při běhu Dynare |

> [!WARNING] Dva technické detaily při práci s `oo_`
> 1. Před extrakcí momentů z `oo_.var` a `oo_.autocorr` je nutné zkontrolovat pořadí proměnných v `M_.endo_names` (např. pokud je inflace 3. proměnná, její rozptyl je `oo_.var(3,3)` a autokorelace 1. řádu `oo_.autocorr{1}(3,3)`).
> 2. Teoretické momenty spočítané v bodě **posteriorního průměru** (*posterior mean*) nejsou totéž jako rozdělení momentů získané integrací přes posteriorní nejistotu parametrů.

---

## 4. Verifikace a porovnání modelů (`NK3` vs. `NK3_hab_simple`)

### Rozšíření o formování návyků (*Habit Formation*)
Porovnáváme základní model `NK3.mod` s modelem `NK3_hab_simple.mod`, který přidává **formování návyků** ($h$) do IS křivky (domácnosti upravují spotřebu postupně):
- **Baseline (`NK3.mod`):**
  $$x_t = E_t x_{t+1} - \frac{1}{\varphi}(r_t - E_t \pi_{t+1}) + g_t$$
- **Simple habit (`NK3_hab_simple.mod`):**
  $$(1 + h) x_t = E_t x_{t+1} + h x_{t-1} - \frac{1 - h}{\varphi}(r_t - E_t \pi_{t+1}) + g_t$$
  - Pro $h = 0$ přechází v původní rovnici.
  - Přidaný zpožděný člen $h x_{t-1}$ může zlepšit zachycení perzistence výstupu, ale **zvyšuje složitost modelu** (nový parametr $h$) a může zhoršit jiné momenty.

**Workflow v MATLABu (`DO_Estimate.m` a `RUN_compare_NK3.m`):**
Skript `RUN_compare_NK3.m` načte již uložené soubory `NK3_results.mat` a `NK3_hab_simple_results.mat` do oddělených struktur (`res_NK3`, `res_NK3_hab_simple`) spolu se stejnými 85 pozorováními – odhad není nutné spouštět znovu.

---

### 4.1 Empirický benchmark 1: Volatilita a relativní volatilita (`compare_std_from_var.m`)
Měří velikost makroekonomických fluktuací pomocí směrodatné odchylky:
$$\sigma_z = \sqrt{\text{Var}(z_t)}, \qquad \sigma_i^{\text{model}} = \sqrt{\Sigma_{ii}^{\text{model}}}$$
A pomocí **relativní volatility** vztažené k růstu výstupu:
$$RV_\pi = \frac{\sigma_{p_{\text{obs}}}}{\sigma_{y_{\text{obs}}}}, \qquad RV_R = \frac{\sigma_{R_{\text{obs}}}}{\sigma_{y_{\text{obs}}}}$$
*Proč sledovat obojí?* Pomáhá to rozlišit, zda má model jen systematicky příliš velké/malé šoky (špatná absolutní, ale dobrá relativní volatilita), nebo zda deformuje poměry fluktuací mezi proměnnými.

| Proměnná | Std. dev. (Data) | Std. dev. (NK3) | Std. dev. (Habit) | Rel. vol. (Data) | Rel. vol. (NK3) | Rel. vol. (Habit) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Output growth** | $0{,}5652$ | $0{,}6388$ | $0{,}6101$ | $1{,}0000$ | $1{,}0000$ | $1{,}0000$ |
| **Inflation** | $0{,}2339$ | $0{,}2201$ | $0{,}2375$ | $0{,}4139$ | $0{,}3445$ | $0{,}3894$ |
| **Interest rate** | $0{,}5022$ | $0{,}4735$ | $0{,}4615$ | $0{,}8885$ | $0{,}7413$ | $0{,}7565$ |

**Interpretace:**
- **Absolutní volatilita:** Model s návyky (*Habit*) lépe vystihuje směrodatnou odchylku růstu výstupu a inflace; základní model (*NK3*) je o něco blíže u nominální úrokové míry.
- **Relativní volatilita:** Model *Habit* je blíže datům jak u inflace, tak u úrokové míry.
- *Pozor:* Dobrá shoda ve volatilitě nezaručuje správné korelace ani perzistenci.

---

### 4.2 Empirický benchmark 2: Současné korelace (`compare_corr_from_var.m`)
Korelace zachycují, zda se proměnné pohybují společně stejným směrem a silou jako v datech:
$$\rho_{ij} = \frac{\text{Cov}(z_i, z_j)}{\sigma_i \sigma_j}, \qquad \mathbf{C}^{\text{model}} = \mathbf{D}^{-1} \mathbf{\Sigma}^{\text{model}} \mathbf{D}^{-1}$$
kde $\mathbf{D}$ je diagonální matice modelových směrodatných odchylek.

| Korelace | Data | NK3 | Habit |
| :--- | :---: | :---: | :---: |
| **Output growth – inflation** | $0{,}0141$ | $0{,}1918$ | $0{,}1137$ |
| **Output growth – rate** | $0{,}1970$ | $-0{,}0177$ | $-0{,}0631$ |
| **Inflation – rate** | $0{,}3825$ | $0{,}3380$ | $0{,}3506$ |

**Interpretace:**
1. Oba modely **nadhodnocují** korelaci mezi růstem výstupu a inflací (ačkoliv *Habit* je blíže datům).
2. Oba správně reprodukují **kladnou** korelaci mezi inflací a úrokovou mírou (*Habit* o něco lépe).
3. **Ani jeden model nezachycuje kladnou korelaci mezi růstem výstupu a úrokovou mírou** (oba produkují záporné znaménko; *NK3* je blíže nule/datům).

---

### 4.3 Empirický benchmark 3: Perzistence a autokorelace (`compare_autocorr.m`)
Měří, jak dlouho fluktuace přetrvávají v čase (porovnání přes prvních 5 čtvrtletních zpoždění):
$$\rho_z(k) = \text{Corr}(z_t, z_{t-k})$$

| Proměnná | Lag 1 (Data) | Lag 1 (NK3) | Lag 1 (Habit) | Lag 5 (Data) | Lag 5 (NK3) | Lag 5 (Habit) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Output growth** | $0{,}4172$ | $-0{,}0951$ | $0{,}0073$ | $-0{,}0885$ | $-0{,}0214$ | $-0{,}0155$ |
| **Inflation** | $0{,}2386$ | $0{,}1891$ | $0{,}3048$ | $0{,}0818$ | $0{,}1091$ | $0{,}1804$ |
| **Interest rate** | $0{,}9788$ | $0{,}9664$ | $0{,}9606$ | $0{,}6559$ | $0{,}8412$ | $0{,}8150$ |

**Interpretace:**
1. Nejsilnější perzistenci v datech vykazuje **úroková míra**.
2. Formování návyků (*Habit*) zlepšuje autokorelaci 1. řádu u růstu výstupu (ze záporné hodnoty na mírně kladnou), oba modely ji však v porovnání s daty ($0{,}4172$) výrazně podhodnocují.
3. U inflace ani jeden model konzistentně nereprodukuje celý profil přes 5 zpoždění.
4. Oba modely dobře zachycují silnou perzistenci úrokové míry na krátkém horizontu, ale na delším horizontu (Lag 5) ji **nadhodnocují**.
$\implies$ *Zavedení formování návyků automaticky nezaručuje lepší shodu s pozorovanou dynamikou ekonomiky.*

---

### 4.4 Bayesovské porovnání modelů (`bayes_model_comparison.m`)
Jednotlivé modely dominují v různých momentech, proto potřebujeme celkové měřítko podpory daty pomocí **marginálních věrohodností** $p(Y \mid M_i)$ (aproximovaných **Laplaceovou metodou**). Marginální věrohodnost průměruje věrohodnost přes všechny hodnoty parametrů vážené jejich priory (tím přirozeně penalizuje zbytečnou složitost modelu).

**Bayesův faktor ($BF_{21}$):**
$$BF_{21} = \frac{p(Y \mid M_2)}{p(Y \mid M_1)} = \exp\big(\log p(Y \mid M_2) - \log p(Y \mid M_1)\big)$$
- $M_1$: základní model (`NK3`), $M_2$: model s návyky (`NK3_hab_simple`).
- $BF_{21} > 1 \implies$ data preferují model s návyky ($M_2$).
- $BF_{21} < 1 \implies$ data preferují základní model ($M_1$).

| Model | Log ML | BF vs NK3 | Post. Prob. (při rovnosti priorů $0{,}5$) |
| :--- | :---: | :---: | :---: |
| **Baseline NK3** | $-38{,}1355$ | $1{,}0000$ | $0{,}9530$ ($95{,}3\,\%$) |
| **Habit NK3** | $-41{,}1449$ | $0{,}0493$ | $0{,}0470$ ($4{,}7\,\%$) |

**Interpretace:**
- Základní model **Baseline NK3 je silně preferován** – evidence v jeho prospěch je přibližně **$20\times$ silnější** ($1 / 0{,}0493 \approx 20{,}3$) a jeho posteriorní pravděpodobnost činí **$95{,}3\,\%$**.
- Přestože *Habit* model zlepšil vybrané empirické momenty (např. volatilitu a lag-1 autokorelaci růstu výstupu), nevede to k vyšší marginální věrohodnosti (penalizace za větší komplexitu parametru $h$ a horší celkové zachycení věrohodnosti dat).

> [!CAUTION] Zásadní omezení Bayesova faktoru
> Bayesův faktor vyjadřuje pouze **relativní** podporu mezi porovnávanými modely, nikoliv **absolutní** adekvátnost modelu. Oba porovnávané modely mohou data popisovat špatně – **nejlepší z porovnávaných modelů nemusí být dobrý model**.

---

## 5. Shrnutí a navazující rozšíření (`NK3_hab_complex.mod`)

### Hlavní závěry cvičení
1. **Odhad a hodnocení modelu jsou dva rozdílné kroky:** Bayesovský odhad najde parametry podmíněné specifikací modelu; empirická verifikace testuje, zda model reprodukuje volatilitu, korelace a perzistenci dat.
2. **Kompromis mezi momenty:** Zlepšení jednoho momentu (např. pomocí *habit formation*) může jít na úkor jiných a nemusí zvýšit marginální věrohodnost modelu.
3. **Komplementarita metod:** Empirické momenty odhalují konkrétní silné a slabé stránky modelu, zatímco Bayesův faktor hodnotí celkovou podporu daty při zohlednění složitosti modelu.

### Další krok k samostatnému vyzkoušení (`NK3_hab_complex.mod`)
Pro otestování bohatší specifikace na stejném vzorku 85 pozorování je k dispozici model `NK3_hab_complex.mod`, který oproti `NK3_hab_simple.mod` navíc zahrnuje:
- formování návyků ve spotřebě,
- cenovou indexaci (*price indexation*),
- bohatší pravidlo měnové politiky,
- explicitní technologické a poptávkové šoky.
```

---

Pokud budeš chtít, můžu z tohoto zápisu (stejně jako u předchozích témat) vygenerovat i sadu Anki kartiček ve formátu `.txt` pro okamžitý import.