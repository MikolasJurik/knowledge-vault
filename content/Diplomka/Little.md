v **poslední sekci tohoto článku (rovnice 10)** Little navrhuje rozšíření svého testu o kovariance ($d^2_{\text{aug}}$), sám k němu ale vyjadřuje skepsi – a přesně z této rovnice o 14–22 let později vyrostly testy Kim & Bentler (2002) a Jamshidian & Jalal (2010).

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Motivace a praktický kontext
Do roku 1988 se testování MCAR dělalo primitivně: pokud chyběla jedna proměnná, udělaly se dvouvýběrové $t$-testy průměrů ostatních proměnných mezi respondenty a nerespondenty. U vícerozměrných dat (program BMDP8D, Dixon 1983) to znamenalo spočítat až $p(p-1)$ vzájemně korelovaných $t$-testů. 
* Little to ukazuje na datech z krevních testů (*Werner data*, $n=188$ žen, 6 proměnných): i když hodnoty umazali čistě náhodně (MCAR), z 20 $t$-statistik vyšly některé extrémní ($-2{,}3; 2{,}4; 2{,}7; 3{,}3$), což by analytika falešně svedlo k zamítnutí MCAR kvůli problému mnohonásobného testování.
* Řešením je **jedna globální testová statistika $d^2$**, která vyhodnotí všechna data najednou (na Wernerových datech vyjde $d^2 = 76{,}5$ při $60$ stupních volnosti, $p = 0{,}074$, tedy MCAR se nezamítá).

### Proč testovat MCAR, když pro odhady (ML) stačí MAR? (Sekce 2)
Little si klade důležitou otázku: maximálně věrohodné (ML) odhady nevyžadují MCAR, stačí jim slabší MAR. Proč tedy MCAR vůbec testovat? Dává **3 důvody** (skvělé do úvodu vaší diplomky!):
1. ==🟡Jednoduché metody (mazání neúplných řádků – *complete-case*, nebo párová analýza) vyžadují MCAR.==
2. ==🟡ML odhad pod vícerozměrnou normalitou (Orchard & Woodbury 1972) zůstává konzistentní i pro nenormální data (pokud mají konečné 4. momenty), **jen pokud jsou data MCAR**. Pod MAR už se drasticky opírá o správnost předpokladu normality.==
3. ==🟡Směrodatné chyby spočítané z *očekávané informační matice* (výpočetně snazší) platí jen pod MCAR; pod MAR je nutné počítat s *pozorovanou informační maticí*.==

*(Navíc poznamenává, že přímé testy MAR známé z ekonometrie – např. Heckmanovy selekční modely – jsou extrémně citlivé na chybnou specifikaci modelu).*

### Matematický princip Littleova testu $d^2$ (Sekce 3)
Rozdělme dataset do $J$ skupin podle unikátních vzorců chybějících dat (včetně kompletních případů). Skupina $j$ má $m_j$ pozorování a $p_j$ pozorovaných proměnných ($\sum m_j = n$).

1. **Pod hypotézou MCAR (Model 2):** Všechny vzorce chybění sdílejí stejný populační vektor průměrů $\mu$ a stejnou kovarianční matici $\Sigma$. Tedy pro pozorovanou část v každém vzorci $j$ platí:
   $$(y_{\text{obs}, i} \mid r_i) \sim N(\mu_{\text{obs}, j}, \Sigma_{\text{obs}, j})$$
2. **Pod alternativní hypotézou (Model 3):** Průměry pozorovaných proměnných $\nu_{\text{obs}, j}$ se mohou v jednotlivých vzorcích lišit, **ale kovarianční matice $\Sigma$ zůstává napříč vzorci stejná**.
3. **Testová statistika $d^2$ (rovnice 5):** Protože $\mu$ a $\Sigma$ neznáme, nahradíme je ML odhady z kompletního modelu pomocí **EM algoritmu** ($\hat{\mu}$ a stupni volnosti korigovaná kovarianční matice $\tilde{\Sigma} = \frac{n}{n-1}\hat{\Sigma}$):
   $$d^2 = \sum_{j=1}^J m_j (\bar{y}_{\text{obs}, j} - \hat{\mu}_{\text{obs}, j}) \, \tilde{\Sigma}_{\text{obs}, j}^{-1} \, (\bar{y}_{\text{obs}, j} - \hat{\mu}_{\text{obs}, j})^T$$
   * Jde v podstatě o vážený součet čtverců Mahalanobisových vzdáleností mezi výběrovým průměrem každého vzorce chybění a celkovým EM odhadem průměru.
4. **Rozdělení statistiky:**
   * **Asymptoticky:** $d^2 \xrightarrow{d} \chi^2_f$, kde počet stupňů volnosti je $f = \sum_{j=1}^J p_j - p$.
   * **Pro malý vzorek a monotónní chybění (Sekce 3.4):** Pokud jsou jen 2 proměnné ($Y_1$ vždy pozorovaná, $Y_2$ částečně chybí), test je algebraicky ekvivalentní klasickému dvouvýběrovému $t$-testu ($d^2 = \frac{n-1}{n-2+F}F$, kde $F = t^2$). Pro obecné monotónní chybění se $d^2$ rozpadá na sumu funkcí nezávislých $F$-statistik z ANCOVA modelů.

### Diskuze rozšíření o kovariance $d^2_{\text{aug}}$ (Sekce 4)
Na žádost recenzenta Little odvodil i věrohodnostní test pro obecnější alternativu (Model 9), kde se mezi vzorci mohou lišit nejen průměry, **ale i kovarianční matice** ($\Gamma_{\text{obs}, j}$). Výsledkem je statistika $d^2_{\text{aug}}$ (rovnice 10) s $f = \sum_j p_j(p_j+3)/2 - p(p+3)/2$ stupni volnosti.
Little však tento směr sám zkritizoval jako prakticky problematický, protože: (1) má příliš mnoho stupňů volnosti (nízká síla), (2) vzorce s $m_j \le p_j$ mají singulární matici $S_{\text{obs},j}$ a musí se vyhodit, (3) podobně jako Bartlettův test rozptylů bude extrémně citlivý na nenormálnost dat.

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Little, R. J. A. (1988). A test of missing completely at random for multivariate data with missing values. *Journal of the American Statistical Association*, 83(404), 1198–1202. https://doi.org/10.1080/01621459.1988.10478722 *(JSTOR: https://www.jstor.org/stable/2290157)*
* **Co testuje:**
  * Testuje **homogenitu vektorů průměrů (HM)** pozorovaných proměnných napříč všemi vzorci chybějících dat.
  * Pokud jsou průměry mezi vzorci významně odlišné ($d^2$ je velké), zamítá se nulová hypotéza MCAR.
* **Jaké má předpoklady:**
  * *Konečné vzorky (odvození přes LRT):* Vícerozměrná normalita dat a **shoda kovariančních matic** napříč vzorci chybění.
  * *Asymptoticky (velké vzorky):* Normalitu lze vypustit! Stačí, aby rozdělení mělo konečné 4. momenty (pro konzistenci $\tilde{\Sigma}$), aby bylo v každém vzorci dostatek pozorování ($m_j \to \infty$, aby zafungoval centrální limitní teorém na průměry $\bar{y}_{\text{obs}, j}$) a aby šly ze všech pozorovaných dvojic proměnných odhadnout všechny prvky matice $\Sigma$.
  * *Typ proměnných:* Určeno pro **kvantitativní (spojité) proměnné** (pro kategorická data odkazuje na kontingenční tabulky a testy podle Fuchse, 1982).
* **Na jakých datech ho autoři testovali:**
  * *Reálná data (ilustrace v Úvodu):* Wernerova krevní data ($n = 188$ žen, $p = 6$ proměnných, ~20 % hodnot smazáno mechanismem MCAR).
  * *Simulační studie (Sekce 3.5 – velmi malá!):*
    * **Velikost vzorku:** Pouze velmi malé soubory $n \in \{20, 40, 80\}$.
    * **Dimenze:** $p = 4$ proměnné s pevně danou korelační strukturou (od $0{,}076$ do $0{,}949$).
    * **3 rozdělení:** Vícerozměrné normální, zešikmené (log-normální) a s těžkými chvosty ($t$-rozdělení se 3 stupni volnosti).
    * **Struktura chybění:** Fixní MCAR (40 % kompletních případů + po 10 % v šesti konkrétních vzorcích chybění $\rightarrow$ vždy min. 2 pozorované proměnné, $f = 15$ stupňů volnosti).
    * **Metrika:** Pouze **chyba I. druhu** (empirická velikost testu na hladinách 20 %, 10 %, 5 %, 1 %) z 1 000 replikací.
* **Hlavní výsledky:**
  * Pro $n = 80$ odpovídá chyba I. druhu velmi dobře nominálním hladinám, a to překvapivě i u log-normálních dat a $t_3$ rozdělení (asymptotická robustnost vůči nenormálnosti se potvrdila už pro $n=80$).
  * Pro velmi malé vzorky ($n = 20, 40$) je test **konzervativní** (zamítá méně často než nominálních 5 %, např. jen ve 2–3,5 % případů).
* **Jaké má slabiny a limity (Skvělé pro vaši diplomku!):**
  1. **Slepota vůči rozdílům v rozptylech a kovariancích:** Alternativní hypotéza předpokládá shodu kovariancí. Pokud MAR/MNAR způsobí chybění na obou koncích rozdělení (např. chudí i nejbohatší neuvádějí příjem, takže průměr věku/vzdělání u chybějících zůstane stejný, ale změní se rozptyl či korelace), Littleův test **vůbec nezachytí porušení MCAR**. (Tím přímo zdůvodníte potřebu testu Jamshidiana a Jalala či `PKLMtest`!).
  2. **Absence studie síly testu (Power) v původním článku:** Little vůbec nesimuloval MAR ani MNAR! Sám v článku přiznává, že jako globální (*omnibus*) test s mnoha stupni volnosti může mít nízkou sílu, a výslovně varuje: *„nezamítnutí nulové hypotézy MCAR neznamená, že MCAR platí“*.
  3. **Citlivost na vysoký počet vzorců chybění (malé $m_j$):** Asymptotický důkaz vyžaduje, aby počet pozorování *v každém vzorci* $m_j$ rostl k nekonečnu. Při velkém $p$ a řídkých vzorcích ($m_j = 1$ nebo $2$) neplatí CLT pro $\bar{y}_{\text{obs}, j}$ a na nenormálních datech pak test může ztrácet přesnost (v reálných datech bývá vzorců mnohem více než 7, které měl Little v simulaci).
  4. **Nevhodnost pro kategorická data:** Pro čistě kategorické nebo smíšené datasety není přirozeně stavěný (převod na dummy proměnné porušuje interpretaci multivariačních průměrů a kovariancí oproti Fuchsovým log-lineárním testům).
  5. **Drobný teoretický nesoulad v simulaci ($t_3$):** Věta v Sekci 3.3 vyžaduje konečné 4. momenty, ale v Sekci 3.5 Little testuje $t$-rozdělení se 3 stupni volnosti, které konečné 4. momenty vůbec nemá (má jen 1. a 2. moment). V diplomce je to pěkný kritický postřeh.
  6. **Nízká síla pod MNAR při slabé korelaci:** Sám Little upozorňuje, že pokud chybění $Y_2$ závisí na $Y_2$ samotném (MNAR), test to dokáže odhalit *jen tehdy*, pokud je $Y_2$ silně korelováno s nějakou pozorovanou proměnnou $Y_1$.

*(Praktický detail pro R kód: Všimněte si, že Little značí v matici indikátorů $r_{ij} = 1$ jako **chybějící** a $0$ jako pozorované – Jamshidian & Jalal to mají přesně obráceně! V R je Littleův test implementován v `naniar::mcar_test()` a `misty::na.test()`.)*

---

## 3. Zřetězená databáze citací (Citation Chain)

### BibTeX záznam (do Zotera / LaTeXu)
```bibtex
@article{little1988test,
  title={A test of missing completely at random for multivariate data with missing values},
  author={Little, Roderick J. A.},
  journal={Journal of the American Statistical Association},
  volume={83},
  number={404},
  pages={1198--1202},
  year={1988},
  publisher={Taylor \& Francis},
  doi={10.1080/01621459.1988.10478722}
}
```

### Řetězec souvisejících prací (jak tento článek propojit s ostatními)

#### A. Historické kořeny a teoretické základy (před Littlem 1988)
* **Rubin, D. B. (1976).** Inference and missing data. *Biometrika*, 63(3), 581–592.
  * *Role:* Formální definice MCAR, MAR a konceptu *ignorability* pro věrohodnostní usuzování.
* **Little, R. J. A., & Rubin, D. B. (1987).** *Statistical Analysis with Missing Data* (1st ed.). Wiley. *(Později 2. vydání 2002 a 3. vydání 2019).*
  * *Role:* Základní učebnice, z níž Little čerpá vlastnosti EM algoritmu, SWEEP operátoru a důsledky MCAR/MAR pro jednoduché metody.
* **Orchard, T., & Woodbury, M. A. (1972).** Missing information principle: Theory and applications. *Proc. 6th Berkeley Symposium*, Vol. 1, 697–715.
  * *Role:* Odůvodnění ML odhadů a rozdílu mezi konzistencí pod MCAR vs. MAR.
* **Efron, B., & Hinkley, D. V. (1978).** Assessing the accuracy of the maximum likelihood estimator: Observed versus expected information. *Biometrika*, 65(3), 457–487.
  * *Role:* Argumentace pro používání pozorované informační matice místo očekávané (důležité pro 3. důvod, proč testovat MCAR).

#### B. Předchůdci a alternativní přístupy zmíněné v článku
* **Dixon, W. J. (Ed.). (1983).** *BMDP Statistical Software*. University of California Press.
  * *Role:* Původní přístup párových $t$-testů (BMDP8D), který Little překonává.
* **Fuchs, C. (1982).** Maximum likelihood estimation and model selection in contingency tables with missing data. *JASA*, 77(378), 270–278.
  * *Role:* Ekvivalent Littleova testu pro **kategorická data** (kontingenční tabulky). Pro vaši práci důležité srovnání při práci se smíšenými/kategorickými ekonomickými daty.
* **Heckman, J. D. (1976).** The common structure of statistical models of truncation, sample selection and limited dependent variables... *Annals of Economic and Social Measurement*, 5, 475–492. (a **Little, 1985**, *Econometrica*, 53, 1469–1474).
  * *Role:* Ekonometrické selekční modely testující MAR/MNAR a ukázka jejich extrémní citlivosti na specifikaci.
* **Simon, G. A., & Simonoff, J. S. (1986).** Diagnostic plots for missing data in least squares regression. *JASA*, 81(394), 501–509.
  * *Role:* Raný regresní přístup k citlivostní analýze odchylek od MCAR u jedné vysvětlující proměnné.

#### C. Přímý vývojový řetězec PO tomto článku (most k vaší diplomce)
1. **Little (1988, eq. 10)** navrhuje, ale nedoporučuje $d^2_{\text{aug}}$ (test průměrů i kovariancí).
2. $\rightarrow$ **Kim, K. H., & Bentler, P. M. (2002)** (*Psychometrika*) na něj reagují a staví GLS testy průměrů a kovariancí (**KB testy**), aby obešli omezení $m_j \ge p_j$.
3. $\rightarrow$ **Jamshidian, M., & Jalal, S. (2010)** (*Psychometrika*, váš první PDF článek) řeší selhávání KB testů při malých $n_i$ a nenormálních datech pomocí Hawkinsova a NP testu.
4. $\rightarrow$ **Li, J., & Yu, Y. (2015)** (*Psychometrika*) a **Spohn et al. (`PKLMtest`)** či **Berrett & Samworth (2023, `MCARtest`)** jdou ještě dál: odstraňují nutnost mazat vzorce s malým $m_j$ a rozšiřují neparametrické testování MCAR na nelineární a smíšená/kategorická data.

---

### Jak tyto dva články už teď krásně využít v R?
Máte teď přečtené dva nejklasičtější články celého oboru (**Little 1988** = test průměrů; **Jamshidian & Jalal 2010** = test kovariancí/normality). Už jen z těchto dvou prací vidíte jeden **geniální simulační scénář**, který můžete dát přímo do svých Tezí:
* Vygenerujte data a vytvořte MAR tak, že chybění bude záviset na kvadratické funkci pozorované proměnné (např. chybí u nejmladších i nejstarších).
* **Hypotéza pro vaši simulaci:** `naniar::mcar_test()` (Little 1988) by měl mít téměř nulovou sílu (průměr zůstane stejný), zatímco `MissMech::TestMCARNormality()` (Jamshidian & Jalal 2010) a `PKLMtest` by měly porušení MCAR odhalit!

Pokud máte připravený další článek (třeba `PKLMtest`, `Li & Yu`, `Molenberghs et al. 2008` nebo `Rhoads 2012`), klidně ho nahrajte a uděláme totéž.