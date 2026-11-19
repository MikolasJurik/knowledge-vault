Tento článek má ve vaší diplomové práci **zcela zásadní strategickou roli**. Zatímco první dva články (Little 1988; Jamshidian & Jalal 2010) navrhují konkrétní statistické testy pro *MCAR*, **Molenberghs et al. (2008)** přinášejí formální **teoretický důkaz o tom, kde leží nepřekročitelná hranice testování** – konkrétně proč **nelze čistě z pozorovaných dat otestovat, zda platí MAR, nebo MNAR**.

Když se vás u obhajoby komise zeptá: *„A proč jste v R netestoval také MAR proti MNAR?“*, tento článek je vaše neprůstřelná odpověď.

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Hlavní problém a motivace
V literatuře se objevila řada modelů pro MNAR data (např. Diggle & Kenward 1994), které umožňují provádět i věrohodnostní testy hypotézy MAR oproti konkrétní alternativě MNAR. Takový test má ale skrytý háček: **platí pouze za předpokladu, že jsme správně uhodli parametrickou rodinu rozdělení MNAR modelu** – a tento předpoklad nelze z dat nijak ověřit.

Autoři proto dokazují obecnou větu (Theorem 1):
> **Každý MNAR model napasovaný na pozorovaná data má svůj jednoznačně určený MAR protějšek, který poskytuje naprosto totožnou shodu s pozorovanými daty (equal fit).**

Oba modely (MNAR i jeho MAR protějšek) dávají stejnou věrohodnost, stejné hodnoty deviačních statistik a stejné predikce pro pozorovanou část dat – **liší se však v tom, co předpovídají pro chybějící (nepozorované) hodnoty**.

### Jak matematický důkaz a konstrukce fungují (Sekce 2–3)
Autoři pracují se třemi hlavními způsoby rozkladu sdružené hustoty dat $Y_i$ a indikátoru chybění $R_i$ (kde $R_{ij}=1$ znamená pozorovanou hodnotu):
1. **Selection Model (SM):** $f(y_i, r_i \mid \theta, \psi) = f(y_i \mid \theta)\, f(r_i \mid y_i, \psi)$ *(standardní Rubinova faktorizace)*.
2. **Pattern–Mixture Model (PMM):** $f(y_i, r_i \mid \theta, \psi) = f(y_i \mid r_i, \theta)\, f(r_i \mid \psi)$ *(rozděluje populaci podle vzorců chybění)*.
3. **Shared Parameter Model (SPM):** $Y_i$ a $R_i$ jsou podmíněně nezávislé při daných náhodných efektech $b_i$.

**Konstrukce MAR protějšku probíhá ve 4 krocích:**
1. Na pozorovaná data se napasuje libovolný MNAR model (získají se odhady $\hat{\theta}, \hat{\psi}$).
2. Výsledný model se přepíše do tvaru **PMM** (rovnice 9):
   $$f(y_i^o, y_i^m, r_i \mid \hat{\theta}, \hat{\psi}) = \underbrace{f(y_i^o \mid r_i, \hat{\theta}, \hat{\psi}) \, f(r_i \mid \hat{\theta}, \hat{\psi})}_{\text{identifikováno z pozorovaných dat}} \cdot \underbrace{f(y_i^m \mid y_i^o, r_i, \hat{\theta}, \hat{\psi})}_{\text{neidentifikováno – dáno jen předpoklady MNAR}}$$
3. **Lemma 1 (Příloha A):** Autoři dokazují, že v rámci PMM platí MAR **tehdy a jen tehdy**, když podmíněné rozdělení chybějících hodnot při daných pozorovaných hodnotách **nezávisí na vzorci chybění $r_i$**:
   $$f(y_i^m \mid y_i^o, r_i, \theta) = f(y_i^m \mid y_i^o, \theta)$$
4. Neidentifikovaný faktor $f(y_i^m \mid y_i^o, r_i, \hat{\theta}, \hat{\psi})$ se nahradí jeho MAR protějškem $h(y_i^m \mid y_i^o) = f(y_i^m \mid y_i^o, \hat{\theta}, \hat{\psi})$ (získaným marginalizací přes vzorce). Protože integrál z $h(y_i^m \mid y_i^o)$ přes $y_i^m$ je roven 1, **věrohodnost pozorovaných dat $\hat{L}$ zůstane matematicky naprosto identická**.

*Technická poznámka:* U monotónního chybění (*dropout*) lze $h(y_i^m \mid y_i^o)$ spočítat přímočaře pomocí tzv. **ACMV omezení** (*available case missing value*, rovnice 12–14). U nemonotónního chybění je nutný iterační výpočet (např. Chen & Fienberg 1974 u kontingenčních tabulek).

### Ukázka na reálných datech: Slovinský plebiscit 1991 (Sekce 4–5)
Autoři metodu demonstrují na slavném datasetu předvolebního průzkumu ve Slovinsku ($N \approx 2\,074$, kategorická data), kde šlo o odhad podílu $\theta$ voličů, kteří budou hlasovat „ano“ pro nezávislost a zároveň se plebiscitu zúčastní (kategorie „nevím“ byla chybějící hodnotou). Výhodou dat bylo, že o 4 týdny později proběhl skutečný plebiscit se známým výsledkem $\theta_{\text{pleb}} = 0{,}885$.

* Na data bylo aplikováno 9 log-lineárních modelů rodiny **BRD1–BRD9** (Baker et al. 1992):
  * **BRD1** je jednoduchý MCAR/MAR model – má však špatnou shodu s pozorovanými daty (log-věrohodnost $-2495{,}29$, ale dává dobrý odhad $\hat{\theta} = 0{,}892$).
  * **BRD6–BRD9** jsou saturované MNAR modely s **dokonalou a navzájem identickou shodou s pozorovanými daty** (log-věrohodnost $-2431{,}06$). Jejich odhady $\hat{\theta}$ se ale dramaticky liší od **0,741** (BRD8) až po **0,867** (BRD9).
* Když autoři z těchto 4 saturovaných MNAR modelů vyrobili jejich **MAR protějšky** (BRD6(MAR)–BRD9(MAR)), všechny poskytly naprosto stejnou perfektní shodu s pozorovanými daty, ale jejich odhad $\hat{\theta}_{\text{MAR}}$ se stabilizoval na jediné hodnotě **0,8919**, která leží těsně u skutečného výsledku plebiscitu ($0{,}885$).
* *Pointa:* Špatný fit jednoduchého parametrického MAR modelu (BRD1) neznamená, že mechanismus není MAR – pouze to, že skutečný MAR model má složitější funkční tvar!

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Molenberghs, G., Beunckens, C., Sotto, C., & Kenward, M. G. (2008). Every missingness not at random model has a missingness at random counterpart with equal fit. *Journal of the Royal Statistical Society: Series B (Statistical Methodology)*, 70(2), 371–388. https://doi.org/10.1111/j.1467-9868.2007.00640.x
* **Co článek zkoumá / dokazuje (Pozor: nejde o statistický test!):**
  * Nejde o detekční test, ale o **teoretický důkaz neidentifikovatelnosti**: pro každý MNAR model existuje MAR protějšek se stejnou věrohodností na pozorovaných datech.
  * Dále konstruuje exaktní algoritmus přechodu z MNAR do MAR pro obecné neúplné kontingenční tabulky (ukazuje, že pro každou neúplnou kontingenční tabulku existuje saturovaný MAR model) a navrhuje tento postup jako součást **citlivostní analýzy** (*sensitivity analysis*).
* **Jaké má předpoklady:**
  * *Obecný teorém (Theorem 1):* Nezávislost mezi subjekty a existence rozumné sdružené hustoty/pravděpodobnosti $f(y_i, r_i \mid \theta, \psi)$ – platí velmi obecně pro selection modely, pattern-mixture modely i shared parameter modely, pro spojitá, longitudinální i diskrétní data.
  * *Parametrická poznámka:* Výsledný MAR protějšek *nemusí patřit do jednoduché, standardní parametrické rodiny* (bývá neparametrický či saturovaný).
  * *Kontingenční tabulky (Sekce 4):* Diskrétní/kategorická data s konečným počtem kategorií; u nemonotónního chybění se předpokládá konvergence iteračního schématu (Chen & Fienberg 1974).
* **Na jakých datech ho autoři testovali:**
  * *Žádná Monte Carlo simulace!* Jde o teoreticko-metodologický článek doplněný jednou detailní případovou studií.
  * *Reálná data:* **Slovenian Public Opinion Survey (1991)** – průzkum veřejného mínění před slovinským referendem o nezávislosti ($N = 2\,074$ respondentů, 3 binární otázky s kategorií „nevím“ interpretovanou jako chybějící údaj; v Sekci 5.2 redukováno na 2 otázky = $2 \times 2$ tabulka se 4 vzorci chybění). Externím validačním benchmarkem byl reálný výsledek plebiscitu ($\theta_{\text{pleb}} = 0{,}885$).
* **Hlavní závěry:**
  * Empirické rozlišení mezi MAR a MNAR bez dodatečných neověřitelných (*a priori*) předpokladů o modelu není možné.
  * Hodnota testů „MAR vs. MNAR“ (např. Diggle & Kenward 1994) je iluzorní, pokud si nejsme předem jisti parametrickou specifikací alternativního modelu.
  * Namísto hledání „jediného testu na MNAR“ je správnou praxí využít MAR analýzu jako základní a doplnit ji o citlivostní analýzu (např. srovnáním MNAR modelů s jejich MAR protějšky nebo pomocí *intervals of ignorance*).
* **Jaké má slabiny, limity a klíčové nuance pro vaši diplomku:**
  1. **Neplatí pro rozlišení MCAR vs. MAR/MNAR:** Toto je klíčové pochopit – Molenberghs et al. neříkají, že nefungují testy *MCAR*! Mechanismy **MCAR vs. ne-MCAR** otestovat lze (a přesně to dělají Little 1988, Jamshidian & Jalal 2010 nebo `PKLMtest`), protože MCAR klade testovatelné podmínky na rozdělení *pozorovaných* hodnot napříč vzorci chybění. Nedokážeme však čistě z dat poznat, zda zamítnutí MCAR způsobil MAR, nebo MNAR.
  2. **Neobvyklost MAR protějšku (mimo standardní parametrické rodiny):** Kritici mohou namítat, že vykonstruovaný MAR protějšek je často „umělý“ (saturovaný nebo neparametrický). Pokud by výzkumník měl z ekonomické teorie 100% jistotu o konkrétní úzké parametrické rodině (např. lineární normální model s přesně danou selekční rovnicí), pak by v rámci této úzké rodiny šlo rozdíly testovat – jak ale víme z ekonometrie (Little 1985), sebemenší odchylka od normality či linearity tyto testy rozbije.
  3. **Praktická ilustrace pouze pro malou kategorickou tabulku:** V článku není ukázána numerická konstrukce MAR protějšku pro vícerozměrná spojitá nebo smíšená ekonomická data ani simulační studie chování odhadů.
  4. **Nutnost externích dat nebo teorie k definitivnímu rozhodnutí:** Na slovinských datech autoři věděli, že MAR odhad ($0{,}892$) byl přesný jen díky tomu, že znali výsledek referenda o měsíc později ($0{,}885$). V běžné ekonomické praxi takový vnější benchmark často nemáme.

*(Poznámka k drobným detailům z článku: v Tabulce 3 autoři mimochodem opravují drobnou výpočetní chybu ze svého staršího článku Molenberghs et al. 2001. A stejně jako Jamshidian & Jalal 2010 značí $R_{ij} = 1$ jako **pozorováno** a $0$ jako chybí – nezapomeňte si v diplomce sjednotit jednotnou notaci pro celou práci!)*

---

## 3. Zřetězená databáze citací (Citation Chain)

### BibTeX záznam (do Zotera / LaTeXu)
```bibtex
@article{molenberghs2008every,
  title={Every missingness not at random model has a missingness at random counterpart with equal fit},
  author={Molenberghs, Geert and Beunckens, Caroline and Sotto, Cristina and Kenward, Michael G.},
  journal={Journal of the Royal Statistical Society: Series B (Statistical Methodology)},
  volume={70},
  number={2},
  pages={371--388},
  year={2008},
  publisher={Wiley Online Library},
  doi={10.1111/j.1467-9868.2007.00640.x}
}
```

### Řetězec souvisejících prací (které z tohoto článku citovat v teoretické kapitole)

#### A. Coarsery, neidentifikovatelnost a teoretické limity testování MAR vs. MNAR
* **Rubin, D. B. (1976).** Inference and missing data. *Biometrika*, 63(3), 581–592.
  * *Role:* Základní definice MCAR, MAR, MNAR a konceptu *ignorability* (rov. 5–6).
* **Gill, R. D., van der Laan, M. J., & Robins, J. M. (1997).** Coarsening at random: characterizations, conjectures and counterexamples. In *Proc. 1st Seattle Symp. Biostatistics*, 255–294.
  * *Role:* Neparametrické a semiparametrické zobecnění MAR (*coarsening at random* – CAR), které ukázalo, že ke každému rozdělení pozorovaných dat existuje CAR model (přímý předchůdce Molenberghsovy věty).
* **Schafer, J. L., & Graham, J. W. (2002).** Missing data: our view of the state of the art. *Psychological Methods*, 7(2), 147–177. *(Pozn.: v referencích článku je překlep v čísle ročníku „2“, správně je to vol. 7).*
  * *Role:* Vynikající přehledový článek, který srozumitelně vysvětluje, proč nelze z dat empiricky otestovat MAR vs. MNAR (vhodné pro vysvětlení intuice v diplomce).

#### B. Modely pro MNAR a původní debata o jejich testování/citlivosti
* **Diggle, P., & Kenward, M. G. (1994).** Informative drop-out in longitudinal data analysis (with discussion). *Applied Statistics*, 43(1), 49–93.
  * *Role:* Slavný model, který pomocí LRT testoval MAR proti MNAR. Diskuze k tomuto článku od **Lairda (1994)**, **Littlea (1994a)** a **Rubina (1994)** odstartovala celou debatu o extrémní citlivosti MNAR modelů na neověřitelné předpoklady.
* **Little, R. J. A. (1993).** Pattern-mixture models for multivariate incomplete data. *JASA*, 88(421), 125–134. & **Little, R. J. A. (1994b).** A class of pattern-mixture models for normal incomplete data. *Biometrika*, 81(3), 471–483.
  * *Role:* Základní práce o *pattern-mixture* modelech (PMM) a potřebě identifikačních omezení pro chybějící parametry.
* **Molenberghs, G., Michiels, B., Kenward, M. G., & Diggle, P. J. (1998).** Monotone missing data and pattern-mixture models. *Statistica Neerlandica*, 52(2), 153–161. (a **Thijs et al., 2002** v *Biostatistics*, 3, 245–265).
  * *Role:* Formulace *available case missing value* (ACMV) omezení – tedy jak přesně vypadá MAR v jazyce pattern-mixture modelů (základ Lemma 1).

#### C. Citlivostní analýza a slovinská studie
* **Rubin, D. B., Stern, H. S., & Vehovar, V. (1995).** Handling “don’t know” survey responses: the case of the Slovenian plebiscite. *JASA*, 90(431), 822–828.
  * *Role:* Původní analýza slovinských dat s využitím výsledku referenda jako empirického důkazu ve prospěch MAR analýzy.
* **Baker, S. G., Rosenberger, W. F., & DerSimonian, R. (1992).** Closed-form estimates for missing counts in two-way contingency tables. *Statistics in Medicine*, 11(5), 643–657.
  * *Role:* Zdroj rodiny log-lineárních modelů BRD1–BRD9 pro $2\times 2$ tabulky s chybějícími okraji.
* **Molenberghs, G., Kenward, M. G., & Goetghebeur, E. (2001).** Sensitivity analysis for incomplete contingency tables: the Slovenian plebiscite case. *Applied Statistics*, 50(1), 15–29.
  * *Role:* Zavedení tzv. *intervals of ignorance* (intervalů nevědomosti) jako alternativy ke klasickým bodovým odhadům pod MNAR.
* **Molenberghs, G., & Kenward, M. G. (2007).** *Missing Data in Clinical Studies*. Wiley.
  * *Role:* Monografie shrnující výběrové modely, PMM a formální citlivostní analýzu.

---

### Jak to propojit do vaší diplomky (Syntéza všech 3 přečtených článků)
Teď už máte pohromadě tři nosné pilíře celé teoretické části diplomky a krásně vidíte, jak na sebe navazují:
1. **Molenberghs et al. (2008)** vám vymezí **hranice problému**: empirické testy nemohou spolehlivě rozhodnout mezi *MAR* a *MNAR* (proto se dostupné statistické testy zaměřují na testování *MCAR* oproti odchylkám od MCAR).
2. **Little (1988)** představuje **klasický test MCAR na úrovni průměrů** (silný pro lineární/posunové odchylky, slepý vůči odchylkám v rozptylech/kovariancích).
3. **Jamshidian & Jalal (2010)** rozšiřují testování MCAR o **homogenitu kovariancí a neparametrickou variantu** (překonávají Littleovu slepotu k rozptylům a citlivost KB testů na normalitu, ale za cenu mazání řídkých vzorců a problémů se šikmými či kategorickými daty).

Chybí vám už jen článek k moderním neparametrickým / ML metodám (např. **Spohn et al. – `PKLMtest`**, **Li & Yu 2015** nebo **Berrett & Samworth 2023 – `MCARtest`**), případně metodika simulací (**Schouten et al. 2018 – `mice::ampute`**) či kritický pohled do praxe (**Rhoads 2012**). Pokud máte další PDF, pošlete ho!