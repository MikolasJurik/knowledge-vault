

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Základní myšlenka a motivace
Dřívější testy mechanismu MCAR (zejména Littleův test, 1988) testovaly především shodu průměrů napříč vzorci chybění (*homogeneity of means*, HM) za předpokladu vícerozměrné normality. Pokud ale v datech chybí hodnoty tak, že se mezi vzorci chybění neliší průměry, ale **rozptyly a kovariance** (např. data chybí u extrémů na obou stranách), Littleův test selže.

Kim a Bentler (2002) proto navrhli GLS testy homogenity kovariancí (**KB testy**), ale ukázalo se, že KB testy selhávají ve dvou situacích:
1. **Když jsou vzorce chybění málo početné ($n_i$ je malé)** – zejména při $n_i = 1$ vzorec nijak nepřispívá k testové statistice, ale nafukuje stupně volnosti, čímž test ztrácí přesnost.
2. **Když data nejsou normální** – u rozdělení s těžkými chvosty KB test falešně zamítá příliš často, u rozdělení s lehkými chvosty téměř nikdy.

Jamshidian a Jalal tento problém řeší chytrým nápadem: **nejprve chybějící data jednou imputují**, čímž získají kompletní datovou matici, a na ni pak aplikují přesný test homogenity kovariancí (HC) pro kompletní data, který pochází od **Hawkinse (1981)** a funguje dobře i pro malá $n_i \ge 4$.

### Dva navržené testy

1. **Modifikovaný Hawkinsův test (parametrický – předpokládá normalitu):**
   * *Imputace:* Chybějící hodnoty se jednorázově stochasticky imputují z podmíněného normálního rozdělení pomocí ML odhadů společného průměru $\hat{\mu}$ a společné kovarianční matice $\hat{\Sigma}$ (rovnice 6).
   * *Testová statistika:* Pro každé pozorování $j$ ve skupině $i$ (kde skupina = konkrétní vzorec chybění) se spočítá Mahalanobisova vzdálenost $F_{ij}$ po vynechání daného pozorování (tzv. *leave-one-out* Hotellingovo $T_{ij}^2$).
   * *Transformace a kombinace:* Pokud platí normalita i shoda kovariancí, pak p-hodnoty $A_{ij} = P(\mathcal{F}_{p, n-g-p} > F_{ij})$ mají **rovnoměrné rozdělení na $(0,1)$**. Místo původního Anderson-Darlingova testu autoři používají silnější **Neymanův test uniformity** ($N_{ik}$ se $k=4$ Legendreovými polynomy, p-hodnoty $P_i$ z 1 milionu simulací) a skupinové p-hodnoty spojí **Fisherovou metodou**: $P_T = \sum_{i=1}^g (-2 \log P_i) \sim \chi^2_{2g}$.
   * *Klíčová vlastnost:* Tento test testuje **současně** homogenitu kovariancí (MCAR) **a** vícerozměrnou normalitu.

2. **Neparametrický test (NP test – nevyžaduje normalitu):**
   * *Imputace:* Aby nebyla narušena distribuce dat umělým předpokladem normality, použijí metodu podle **Srivastavy (2002)**: chybějící hodnotu předpoví pomocí nejlepšího lineárního prediktoru (z kompletních případů, případně ML odhadů) a přičtou náhodnou chybu převzorkovanou (bootstrapem) ze škálovaných reziduí kompletních pozorování (rovnice 7).
   * *Test:* Na takto doplněných datech spočítají stejné statistiky $F_{ij}$. Protože u nenormálních dat neznají jejich přesné teoretické rozdělení, testují pouze to, zda mají $F_{ij}$ ve všech $g$ skupinách **stejné rozdělení** – k tomu použijí neparametrický **$k$-výběrový Anderson-Darlingův test** (Scholz Tady je podrobné zpracování článku rozdělené přesně do tří částí, o které jste si řekl: ￼￼výcuc￼￼, ￼￼strukturované zápisky￼￼ a ￼￼zřetězená databáze citací￼￼ (připravená pro Zotero i pro vaši rešeršní tabulku).

￼￼(Poznámka k formátu přiloženého PDF: Jde o rukopisnou/preprintovou verzi z repozitáře UCLA eScholarship z let 2010/2011. Finální recenzovaná verze vyšla v časopise ￼￼Psychometrika, 75(4), 649–674 (2010)￼￼ a autoři na jejím základě později vytvořili R balíček ￼￼￼￼MissMech￼￼￼￼.)￼& Stephens, 1987).

### Sekvenční testovací procedura (Obrázek 7 v článku)
Protože nevíme, zda jsou reálná data normální, autoři doporučují tento dvoustupňový postup:
1. Spusťte **Hawkinsův test**.
   * *Nezamítá se?* $\rightarrow$ Nejsou důkazy proti normalitě ani proti MCAR. Konec.
2. *Zamítá se?* Můžete pro data předpokládat normalitu?
   * *Ano:* $\rightarrow$ Zamítněte MCAR.
   * *Ne:* Spusťte **NP test**.
     * *NP se nezamítá:* $\rightarrow$ Data jsou **nenormální**, ale homoskedastická (není důvod zamítnout MCAR).
     * *NP se zamítá:* $\rightarrow$ Zamítněte **MCAR** (o normalitě nelze rozhodnout).

### Rozšíření o vícenásobnou imputaci (Sekce 6)
Jednorázová imputace vnáší do výsledku náhodnost (při jiném seedu může vyjít mírně jiná p-hodnota). Autoři proto navrhují data imputovat např. 200krát:
* Výsledné p-hodnoty nelze jednoduše sloučit Fisherovým testem (imputované datasety nejsou nezávislé), ale lze vykreslit **histogram celkových p-hodnot** a sledovat podíl zamítnutí.
* Navíc lze vykreslit **boxploty skupinových statistik** ($P_i$ u Hawkinse, $T_i$ u NP testu) a přímo diagnostikovat, **který konkrétní vzorec chybění** má odlišnou kovarianční matici (Obrázky 4–6).

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Jamshidian, M., & Jalal, S. (2010). *Tests of homoscedasticity, normality, and missing completely at random for incomplete multivariate data* (UCLA Department of Statistics Papers). https://escholarship.org/uc/item/2ww0g4bx *(finální verze: Psychometrika, 75(4), 649–674)*.
* **Co testuje:**
  * Primárně testuje **homogenitu kovariančních matic (HC / homoskedasticitu)** napříč skupinami danými identickými vzorci chybějících dat.
  * Sekundárně (v kombinaci obou testů) testuje **vícerozměrnou normalitu**.
  * V Příloze 8.2 autoři dokazují větu, že za homogenity ostatních parametrů $\theta$ (např. průměrů) implikuje homogenita kovariancí mechanismus **MCAR**. Zamítnutí HC tedy vede k zamítnutí MCAR.
* **Jaké má předpoklady:**
  * *Společné předpoklady pro oba testy:*
    * Pozorování jsou nezávislá a pocházejí z **jedné homogenní populace** (pokud kompletní data sama o sobě obsahují více skupin s různými kovariancemi, test chybně zamítne MCAR).
    * Ostatní parametry rozdělení $\theta$ (zejména **průměry**) jsou napříč vzorci chybění stejné (pokud se liší pouze průměry a kovariance zůstanou stejné, testy HC to nemusí zachytit – proto autoři v textu odkazují na kombinaci s testy shody průměrů, jako je Littleův HM test).
    * Každý testovaný vzorec chybění musí mít dostatečný počet pozorování $n_i$ (Hawkinsův test vyžaduje $n_i \ge 4$, v simulacích mazali vzorce s $n_i \le 6$).
  * *Specifické pro Hawkinsův test:* Striktní **vícerozměrná normalita**.
  * *Specifické pro NP test:* Spojitá distribuční funkce a **lineární vztahy mezi proměnnými** (kvůli způsobu Srivastavovy lineární imputace reziduí).
* **Na jakých datech ho autoři testovali (Design simulací):**
  * *Rozsah a dimenze:* $n \in \{200, 500, 1000\}$, počet proměnných $p \in \{4, 7, 10\}$, míra chybění $q \in \{10\%, 20\%, 30\%\}$, vždy 1 000 replikací na hladině $\alpha = 0{,}05$.
  * *8 rozdělení kompletních dat:* standardní normální ($N$), korelované normální ($\text{Corr-}N$ s faktorovou strukturou), multivariační $t_4$ a $\text{Corr-}t_4$ (těžké chvosty), uniformní $U(0,1)$ a $\text{Corr-}U$ (lehké chvosty), $W = N + 0{,}1N^3$ (těžké chvosty) a nezávislý $\text{Weibull}(1,2)$ (pravostranně zešikmené).
  * *Testované alternativy (síla testu):*
    1. **MAR:** $Y_{ij}$ chybí, pokud předchozí proměnná $Y_{i, j-1}$ překročí určitý práh.
    2. **non-HC:** Data rozdělena pod MCAR, následně v nejpočetnějším neúplném vzorci nahrazena jinou kovarianční maticí (např. $\mathcal{N}(0,I)$ vs. $\mathcal{N}(0,\Sigma)$).
  * *Vícenásobná imputace (Sekce 6):* Ilustrativní příklady na $n=300$ a $n=500$, $p=7$, $q=20\%$ (normální data a $t_4$), 200 imputací.
* **Hlavní výsledky:**
  * U normálních dat drží Hawkinsův test chybu I. druhu velmi přesně u 5 % a má dobrou sílu pro $n \ge 500$.
  * U nenormálních dat KB test i Hawkinsův test jako testy MCAR zcela selhávají, zatímco NP test dosahuje výrazně lepších chyb I. druhu i slušné síly (zejména pro $n = 500$ a $1000$).
* **Jaké má slabiny a limity (Zlatý důl pro vaši diplomku!):**
  1. **Problém malých vzorců chybění a mazání dat ("NED"):** Při vyšším počtu proměnných ($p=10$) a vyšším chybění ($q=20\text{–}30\%$) prudce roste počet unikátních vzorců chybění s $n_i \le 6$. Jejich vyřazením autoři ztratili přes 50 % souboru a v tabulkách mají místo výsledků *"NED" (Not Enough Data)*. V reálných ekonomických dotaznících s mnoha proměnnými to může test zcela znefunkčnit.
  2. **Inflace chyby I. druhu u NP testu:** Protože statistiky $F_{ij}$ uvnitř skupin sdílejí odhady $\bar{X}_i$ a $S$, nejsou striktně nekorelované. U větších $p$, $q$ nebo u **zešikmených dat (Weibull)** a těžkých chvostů ($t$) roste chyba I. druhu NP testu nad nominálních 5 % (v Tabulce 3 až na **20–33 %**!). Pro šikmá ekonomická data (příjmy) je to klíčové varování.
  3. **Nízká síla Hawkinsova testu proti zešikmení (Weibull):** Jako test normality má Hawkinsův test proti Weibullovu rozdělení překvapivě nízkou sílu (jen 6–28 %).
  4. **Nemožnost pracovat s kategorickými a nelineárními daty:** Srivastavova imputace v NP testu předpokládá spojitost a linearitu. Nelze ji přímočaře použít pro faktorové/diskrétní socioekonomické proměnné (pohlaví, vzdělání, region).
  5. **Autoři vůbec netestovali MNAR:** V celé simulační studii chybí scénář MNAR, a rovněž netestovali scénář MAR s čistým nelineárním tvarem (např. U-tvar), kde průměry zůstanou identické.
  6. **Chybějící formální agregace p-hodnot u MI:** Při vícenásobné imputaci neumí spojit p-hodnoty do jednoho čísla, dávají jen grafickou heuristiku.

---

## 3. Zřetězená databáze citací (Citation Chain)

Níže máte připravený **BibTeX záznam** pro tento článek (abyste si ho mohl rovnou nahrát do Zotera / LaTeXu) a přehled nejdůležitějších navazujících studií citovaných přímo v tomto článku, rozdělených podle jejich role ve vaší práci.

### BibTeX záznam (do Zotera / R Markdownu / LaTeXu)
```bibtex
@article{jamshidian2010tests,
  title={Tests of homoscedasticity, normality, and missing completely at random for incomplete multivariate data},
  author={Jamshidian, Mortaza and Jalal, Siavash},
  journal={Psychometrika},
  volume={75},
  number={4},
  pages={649--674},
  year={2010},
  publisher={Springer},
  note={UCLA Preprint available at https://escholarship.org/uc/item/2ww0g4bx}
}
```

### Řetězec souvisejících prací (co z tohoto článku vytěžit do rešerše)

#### A. Přímí předchůdci a konkurenti v testování MCAR
* **Rubin, D. B. (1976).** Inference and missing data. *Biometrika*, 63(3), 581–592.
  * *Role:* Prvotní formální definice MCAR, MAR, MNAR. Základní kámen teoretické kapitoly.
* **Little, R. J. A. (1988).** A test of missing completely at random for multivariate data with missing values. *JASA*, 83(404), 1198–1202.
  * *Role:* Hlavní srovnávací benchmark (HM test průměrů). Autoři zmiňují, že jeho LRT varianta pro kovariance (HMC) vyžaduje $n_i \ge p_i$ a extrémně velké $n$.
* **Kim, K. H., & Bentler, P. M. (2002).** Tests of homogeneity of means and covariance matrices for multivariate incomplete data. *Psychometrika*, 67(4), 609–624.
  * *Role:* Přímý konkurent (KB GLS testy HM, HC, HMC), jehož slabiny (malá $n_i$, nenormálnost) tento článek překonává.
* **Bentler, P. M., Kim, K. H., & Yuan, K. H. (2004).** Testing homogeneity of covariances with infrequent missing data patterns. *Unpublished Manuscript*.
  * *Role:* Diagnostika selhání KB testu při malých $n_i$ (zejména $n_i = 1$). Doporučili vzorce s malým $n_i$ mazat.
* **Yuan, K. H., Bentler, P. M., & Zhang, W. (2005).** The effect of skewness and kurtosis on mean and covariance structure analysis. *Sociological Methods & Research*, 34(2), 240–258.
  * *Role:* Vysvětluje matematický důvod, proč KB test selhává při šikmosti a špičatosti (zkreslení studentizační matice).
* **Jamshidian, M., & Schott, J. (2007).** Testing equality of covariance matrices when data are incomplete. *Computational Statistics and Data Analysis*, 51(9), 4227–4239.
  * *Role:* Přehled starších asymptotických testů homogenity kovariancí pro neúplná data.

#### B. Matematické stavební kameny navržených testů
* **Hawkins, D. M. (1981).** A new test for multivariate normality and homoscedasticity. *Technometrics*, 23(1), 105–110.
  * *Role:* Původní test pro kompletní data. *(Pozor – zajímavý detail do teoretické části: Jamshidian a Jalal v Příloze 8.1 odhalili algebraickou chybu na straně 106 původního Hawkinsova článku!)*
* **Neyman, J. (1937).** "Smooth" test for goodness of fit. *Skandinavisk Aktuarietidskrift*, 20, 150–199. (a **Marhuenda, Y., Morales, D., & Pardo, M. C., 2005** v *Statistics*, 39, 315–328).
  * *Role:* Test uniformity na $(0,1)$ pomocí Legendrových polynomů, kterým nahradili původní Anderson-Darlingův test.
* **Fisher, R. A. (1932).** *Statistical Methods for Research Workers* (4th ed.).
  * *Role:* Klasická metoda spojování nezávislých p-hodnot pomocí $\chi^2_{2g}$.
* **Scholz, F. W., & Stephens, M. A. (1987).** $K$-sample Anderson-Darling tests. *JASA*, 82(399), 918–924.
  * *Role:* Jádro neparametrického (NP) testu pro porovnání distribucí $F_{ij}$ mezi skupinami (v R např. přes `kSamples::ad.test`, interně v `MissMech`).

#### C. Imputační metody použité uvnitř testů
* **Jamshidian, M., & Bentler, P. M. (1999).** ML estimation of mean and covariance structures with missing data using complete data routines. *JEBS*, 24(1), 21–41.
  * *Role:* Výpočet ML odhadů $\hat{\mu}, \hat{\Sigma}$ pro parametrickou imputaci v Hawkinsově testu.
* **Srivastava, M. S. (2002).** *Methods of Multivariate Statistics* (Kapitola 18). Wiley. & **Srivastava, M. S., & Dolatabadi, M. (2009).** Multiple imputation and other resampling scheme for imputing missing observations.
  * *Role:* Zdroj neparametrické bootstrapové imputace reziduí v NP testu i jeho vícenásobné variantě.

---

### Další doporučený krok pro vás
Pokud chcete pokračovat v započatém plánu:
1. Zkuste si v R nainstalovat balíček **`MissMech`** a spustit `MissMech::TestMCARNormality()` na jednoduchých datech (např. s normálním a pak s log-normálním rozdělením) – uvidíte v praxi přesně ty dvě statistiky (Hawkinsovu a NP), které jste si právě nastudoval, včetně toho, jak balíček automaticky zahazuje vzorce s $n_i \le 6$ (parametr `del.lesscases`).
2. Jako další článek do rešerše si vezměte buď klasiku **Little (1988)** (pro srovnání průměrů vs. kovariancí), nebo moderního nástupce **Spohn et al. (`PKLMtest`)**, který řeší právě tu největší slabinu Jamshidiana a Jalala – tedy práci s **kategorickými/smíšenými daty a velkým $p$ bez nutnosti mazat vzorce s malým $n_i$**.