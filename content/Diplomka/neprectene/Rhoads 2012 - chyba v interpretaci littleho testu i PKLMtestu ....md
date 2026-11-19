Tento článek je pro vaši diplomovou práci na **Katedře ekonomie** mimořádně cenný – autor ho píše přímo pro oblast kvantitativní analýzy veřejných politik a ekonometrie (cituje Heckmana, Manskiho, Winshipa a Marea). Navíc jste si určitě všiml, že na něj přímo odkazuje i **Spohn et al. (2025, `PKLMtest`)** na stranách 281 a 285!

Rhoads (2012) totiž pojmenovává **nejčastější logickou chybu**, kterou dělají výzkumníci (a dokonce i statistické učebnice a software SPSS), když interpretují výsledky testů jako Littleův test (`mcar_test`) nebo `PKLMtest`.

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Hlavní teze: MCAR = MAR $\land$ OAR
Rhoads vychází ze stejného problému jako Molenberghs et al. (2008) – tedy že hypotézu MAR nelze čistě z pozorovaných dat otestovat – ale posouvá argumentaci o krok dál přímo na **testy MCAR** (Sekce 2.1–2.2).

Pomocí Bayesovy věty rozkládá definici MCAR (rovnice 8–10) na dvě samostatné podmínky:
1. **MAR:** $P(\mathbf{Y}_{mis} \mid \mathbf{Y}_{obs}, \mathbf{R}) = P(\mathbf{Y}_{mis} \mid \mathbf{Y}_{obs})$ — *z dat zcela netestovatelná* (data dávají stejnou podporu MAR i MNAR).
2. **OAR (*Observed At Random*, Rubin 1976):** $P(\mathbf{Y}_{obs} \mid \mathbf{R}) = P(\mathbf{Y}_{obs})$ — tedy že rozdělení *pozorovaných* dat nezávisí na vzorci chybění $\mathbf{R}$. Tato podmínka **testovatelná je**.

Z toho plyne klíčový závěr:
> **Všechny „testy MCAR“ (Littleův test, PKLMtest apod.) ve skutečnosti testují pouze podmínku OAR – tedy nutnou, nikoli postačující podmínku pro MCAR!**

* **Proč to vadí?** Čistě MNAR proces, kde chybění závisí *výhradně* na nepozorovaných hodnotách $P(\mathbf{R} \mid \mathbf{Y}_{obs}, \mathbf{Y}_{mis}) = P(\mathbf{R} \mid \mathbf{Y}_{mis})$ (rovnice 5), může zanechat pozorovaná data naprosto homogenní (OAR platí!). Test MCAR pak nevyjde signifikantně ($p > 0{,}05$), ale **data přesto nejsou MCAR** (jsou MNAR!).
* Testy MCAR tedy dokážou MCAR **vyvrátit** (falzifikovat), ale **nikdy jej nemohou dokázat**. Smysl mají hlavně tehdy, pokud už *předem (a priori)* věříte, že platí MAR, a chcete jen otestovat, zda navíc platí i přísnější MCAR.

### Tři chyby v literatuře a kritika „testů MAR“ (Sekce 3)
Rhoads prochází publikované studie i učebnice a rozděluje testy do tří skupin:
1. **Testy MCAR / OAR (Little 1988, Chen & Little 1999, Lipsitz et al. 1994):**
   * Ukazuje alarmující příklady z praxe (str. 8–9): mnoho empirických článků použije *nesignifikantní* Littleův test ($p > 0{,}05$) jako jediný „důkaz“, že data jsou MCAR a nezávisejí na nepozorovaných hodnotách (např. Steginga et al. 2005; Kunina et al. 2007). Jiné studie zase po *signifikantním* Littleově testu prohlásí data za „neignorabilní (MNAR)“ (Lang et al. 2007) – přestože zamítnutí může znamenat „jen“ MAR! Zmatení podle něj podporuje i nápověda SPSS (2008).
2. **Parametrické testy MAR vs. konkrétní MNAR (např. Zhou et al. 1999):**
   * Závisí na neověřitelné parametrické specifikaci MNAR modelu (odkazuje na Molenberghs et al. 2008).
3. **Domnělé testy „MAR“ nebo „ignorability“ (Katz 2006; Sherman 2000; Park & Davis 1993; Donaldson & Moinpour 2005):**
   * Rhoads dokazuje, že všechny tyto postupy (včetně logistické regrese indikátoru chybění na pozorované proměnné podle Katze, 2006) ve skutečnosti testují **OAR** nebo **podmíněné OAR (COAR)** – tedy $f(\mathbf{Y}_{obs} \mid \mathbf{X}_L, \mathbf{R}) = f(\mathbf{Y}_{obs} \mid \mathbf{X}_L)$ (rovnice 18).
   * V **Příloze (Tabulky 1–2)** konstruuje brilantní protipříklad k testu Parka a Davise (1993): u hypotetického protikuřáckého programu vycházejí pozorované podíly kuřáků u kompletních i neúplných případů na desetinu stejně ($Q_c = 0, p = 1{,}00$, test hlásí „100% ignorabilní“), zatímco ve skutečných (latentních) datech v kontrolní skupině odpadli kuřáci a v intervenční nekuřáci – jde o silný **MNAR**, který obrátil skutečný efekt programu!

### Co dělat místo slepé víry v testy? (Sekce 4 – Pohled ekonometrie)
Protože i MAR je velmi silný předpoklad (vybírá jeden konkrétní model z nekonečně-rozměrného prostoru mechanismů!), Rhoads shrnuje 3 alternativní přístupy:
1. **Explicitní MNAR modely (Selection models & Pattern-mixture models):** Např. Heckmanův selekční model (1979) – vyžaduje ale netestovatelnou normalitu nebo netestovatelnou *exclusion restriction* (Winship & Mare 1992).
2. **Neparametrické meze (Manski bounds, 1989–2007):** Nepředpokládá *nic* o mechanismu chybění a spočítá nejhorší/nejlepší možný scénář (*worst-case bounds*, např. všichni chybějící = 0 vs. = 1). U binárních dat v ekonomii práce (Pepper 2000; Gonzalez 2005) to dává „oblast konsenzu“, u spojitých vícerozměrných dat jsou ale meze často příliš široké/neinformativní.
3. **Citlivostní analýza (*Sensitivity analysis*):** Nejlepší střední cesta, **pokud** se dělá dobře – tzn. neměnit abstraktní statistické parametry (jako Raghunathan 2000) a netvrdit, že shoda 2 MAR a 2 MNAR modelů dokazuje nezkreslenost (Fairclough et al. 1998 – všechny 4 modely mohou být zkreslené stejným směrem!). Doporučuje hledat **bod zvratu (*tipping point*)** u věcně interpretovatelného parametru, kde se změní znaménko či signifikance ekonomického efektu, a zeptat se expertů, zda je taková hodnota realistická.

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Rhoads, C. H. (2012). Problems with tests of the missingness mechanism in quantitative policy studies. *Statistics, Politics, and Policy*, 3(1), Article 6, 1–23. https://doi.org/10.1515/2151-7509.1012
* **Co článek zkoumá (Nejde o nový test, ale o metodologickou kritiku):**
  * Zkoumá, co ve skutečnosti testují publikované testy MCAR, MAR a ignorability.
  * Dokazuje, že testy MCAR testují pouze nutnou podmínku **OAR** ($P(\mathbf{Y}_{obs}\mid \mathbf{R}) = P(\mathbf{Y}_{obs})$), a testy „MAR/ignorability“ (Katz 2006; Park & Davis 1993; Donaldson & Moinpour 2005) testují jen OAR nebo **podmíněné OAR (COAR)**.
* **Jaké má předpoklady:**
  * Jde o teoreticko-konceptuální analýzu postavenou přímo na obecné Rubinově (1976) pravděpodobnostní definici MCAR/MAR/MNAR a Bayesově větě – nevyžaduje žádné parametrické ani distribuční předpoklady.
  * Konkrétní analýza testu Parka a Davise (1993) v Příloze využívá model GSK (Grizzle, Starmer & Koch, 1969) pro kategorická opakovaná měření.
* **Na jakých datech ho autor testoval:**
  * *Žádné Monte Carlo simulace ani reálný empirický dataset.*
  * **Literární audit:** Systematický rozbor učebnic, nápovědy SPSS (2008) a více než desítky aplikovaných studií z oblasti politologie, psychologie, vzdělávání a medicíny, které testy nesprávně interpretovaly.
  * **Konstruovaný hypotetický protipříklad (Příloha, Tabulky 1–2):** $2\times 2$ longitudinální kontingenční tabulka (protikuřácký vzdělávací program, *Treat vs. Control*, čas $t=1, 2$), kde latentní MNAR generuje pozorovaná data s $Q_c = 0$ a $p = 1{,}00$.
* **Hlavní výsledky a přínosy pro ekonomickou diplomku:**
  * Přesné pojmové oddělení **OAR vs. MCAR** (které dnes přebírá i nejnovější statistická literatura včetně `PKLMtest`, Spohn et al. 2025).
  * Jasná metodologická pravidla pro interpretaci testů: *zamítnutí* = důkaz proti MCAR (ale ne důkaz MNAR!); *nezamítnutí* = pouze nedostatek důkazů proti OAR (nikoli důkaz MCAR!).
  * Skvělý ekonometrický přesah: propojení tématu s **pomocnými proměnnými** (*auxiliary variables* dokážou převést MNAR na MAR, str. 3), **Heckmanovým modelem**, **Manskiho mezemi** a **tipping-point citlivostní analýzou**.
* **Jaké má slabiny a limity:**
  1. **Chybí numerická/simulační studie:** Článek je čistě konceptuální; nijak netestuje empirickou sílu ani chybu I. druhu dostupných testů v různých situacích.
  2. **Zastaralý záběr konkrétních testů MCAR:** V Sekci 3.1 zmiňuje pouze testy založené na průměrech (Little 1988; Chen & Little 1999) nebo kategorické testy (Park & Davis 1993). Ačkoliv krátce poznamenává, že by šlo srovnávat i jiné charakteristiky rozdělení než průměry (str. 7), ještě nezachycuje moderní neparametrické testy celých rozdělení (Jamshidian & Jalal 2010; Li & Yu 2015; Berrett & Samworth 2023; Spohn et al. 2025) – a hlavně **neřeší Berrettovu (2023) podmínku kompatibility**, která je přesnější než samotné OAR, pokud chybějí kompletní případy.
  3. **Příliš defenzivní postoj k testům MCAR:** Autor správně varuje před falešnou jistotou při *nezamítnutí* MCAR, ale poněkud zastiňuje praktickou hodnotu *zamítnutí* OAR: v ekonomických datech (a zvlášť před nasazením rychlé *listwise deletion* nebo průměrové imputace) je schopnost spolehlivě zachytit porušení OAR mimořádně užitečným prvním filtrem – přesně jak ostatně potvrzuje i váš případ z R simulací!
  4. **Drobné citační nesrovnalosti v originálu:** V textu na str. 14 cituje „Gonzalez (2006)“ a „Scharfstein, Manski and Anthony (2003)“, zatímco v seznamu literatury jsou roky 2005 a 2004 (dobré vědět, abyste ty překlepy neopsal).

---

## 3. Zřetězená databáze citací (Citation Chain)

### BibTeX záznam (do Zotera / LaTeXu)
```bibtex
@article{rhoads2012problems,
  title={Problems with tests of the missingness mechanism in quantitative policy studies},
  author={Rhoads, Christopher H.},
  journal={Statistics, Politics, and Policy},
  volume={3},
  number={1},
  pages={Article 6},
  year={2012},
  publisher={De Gruyter},
  doi={10.1515/2151-7509.1012}
}
```

### Řetězec souvisejících prací (které z tohoto článku vytěžit – zejména pro ekonomii!)

#### A. Ekonometrické alternativy a částečná identifikace (skvělé do Diskuze DP)
* **Manski, C. F. (1989).** Anatomy of the selection problem. *Journal of Human Resources*, 24(3), 343–360. *(A navazující knihy: Manski 1995, 2003, 2007).*
  * *Role:* Základ neparametrických mezí (*worst-case bounds*) v ekonometrii bez nutnosti testovat MAR/MNAR.
* **Heckman, J. (1979).** Sample selection bias and specification error. *Econometrica*, 47(1), 153–161. & **Winship, C., & Mare, R. D. (1992).** Models for sample selection bias. *Annual Review of Sociology*, 18, 327–350.
  * *Role:* Klasický ekonometrický selekční model pro MNAR a přehled jeho vysoké citlivosti na normalitu a *exclusion restrictions*.
* **Pepper, J. (2000)** (*Review of Economics and Statistics*, 82(3), 472–488) & **Gonzalez, L. (2005)** (*Journal of Applied Econometrics*, 20(6), 771–795).
  * *Role:* Konkrétní aplikace Manskiho mezí v empirické ekonomii (přenos sociálních dávek, dopad jazykových dovedností na trh práce).

#### B. Ostatní testy a heuristiky kritizované v článku (hodí se zmínit v přehledu metod!)
* **Katz, M. H. (2006).** *Multivariable Analysis: A Practical Guide for Clinicians* (2nd ed.). Cambridge University Press.
  * *Role:* Zdroj populární **logistické regrese indikátoru chybění** ($R \in \{0,1\}$) na pozorované proměnné – heuristiky, kterou mnozí analytici používají k „testování MAR“. Rhoads jasně dokazuje, že jde jen o test **OAR**! *(Skvělá poznámka do kapitoly o metodách v R, pokud do srovnání zařadíte i jednoduchou logistickou regresi!).*
* **Chen, H. Y., & Little, R. (1999).** A test of missing completely at random for generalised estimating equations with missing data. *Biometrika*, 86(1), 1–13.
  * *Role:* Neparametrické rozšíření Littleova testu pro GEE modely (Rhoads zdůrazňuje, že stejně jako Little testuje jen OAR).
* **Park, T., & Davis, C. S. (1993).** A test of the missing data mechanism for repeated categorical data. *Biometrics*, 49(2), 631–638. (a **Park & Lee, 1997**).
  * *Role:* Test kategorických opakovaných měření přes Waldovu statistiku, na němž Rhoads v Příloze ukazuje rozdíl mezi **COAR** (podmíněným OAR) a pravou ignorabilitou.
* **Lipsitz, S. R., Laird, N. M., & Harrington, D. P. (1994).** Weighted least squares analysis of repeated categorical measurements with outcomes subject to nonresponse. *Biometrics*, 50(1), 11–24.
  * *Role:* Explicitní LRT test MCAR vs. MAR pro kategorická longitudinální data.
* **Donaldson, G. W., & Moinpour, C. M. (2005).** Learning to live with missing quality-of-life data... *Journal of Clinical Oncology*, 23(30), 7380–7384.
  * *Role:* Grafická diagnostika průměrů napříč vzorci (další případ testování jen COAR).

#### C. Přímé propojení s vašimi předchozími 4 články
* **Rubin (1976)** a **Little & Rubin (2002)** $\rightarrow$ definice OAR, MAR, MCAR a *parameter distinctness*.
* **Molenberghs et al. (2008)** $\rightarrow$ Rhoads ho cituje v Sekci 3.2 jako formální důkaz, proč testy typu Zhou et al. (1999) nefungují bez *a priori* víry v MNAR model.
* **Spohn et al. (2025, `PKLMtest`)** $\rightarrow$ přímo cituje Rhoadse (2012) pro přesnou definici toho, že neparametrické testy MCAR ve skutečnosti testují **OAR**.

---

### Shrnutí vašeho „arzenálu“ (6 přečtených článků – pokud počítáme i Van Buurena)
V tuto chvíli už máte **kompletní teoretický i metodologický základ** pro napsání jak Tezí DP, tak celé literární rešerše:
* **Teoretická nemožnost & správná interpretace:** Rubin (1976) $\rightarrow$ Molenberghs et al. (2008) [MAR vs. MNAR] + Rhoads (2012) [OAR vs. MCAR] + Berrett & Samworth (2023) [kompatibilita vs. MCAR].
* **Klasické testy v R:** Little (1988) [`naniar`] + Jamshidian & Jalal (2010) [`MissMech`].
* **Moderní neparametrické/ML testy v R:** Berrett & Samworth (2023) [`MCARtest`] + Spohn et al. (2025) [`PKLMtest`].

Jako poslední praktický dílek do skládačky už zbývá jen metodika simulací (**Schouten, Lugtig & Vink 2018** o funkci `mice::ampute()`, případně **Yuan et al. 2018**), a pak už se můžete rovnou vrhnout na sepisování **Tezí DP a oficiálního zadání** pro vedoucího! Chcete ještě zpracovat nějaký článek, nebo už jdeme připravit čistopis Tezí DP?