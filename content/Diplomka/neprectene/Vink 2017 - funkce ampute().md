Tento poslední článek je **metodologický manuál pro vaši praktickou část v R**. Na rozdíl od předchozích studií neřeší, jak chybění *testovat*, ale **jak ho v simulacích správně *vytvořit*** (tzv. *amputace* dat), abyste u obhajoby dokázal obhájit design své Monte Carlo studie. 

Právě funkci **`mice::ampute()`** představenou v tomto článku použili i Spohn et al. (2025) pro validaci `PKLMtest` na reálných datech!

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Problém dosavadní praxe: Postupná jednorozměrná amputace (SUA)
Většina starších simulačních studií vytvářela chybějící hodnoty proměnnou po proměnné nezávisle (*stepwise univariate amputation*, SUA): nejprve umazali $Y_1$, pak nezávisle $Y_2$ atd. Schouten, Lugtig a Vink ukazují **dvě vážné chyby** tohoto postupu:
1. **Ztráta kontroly nad podílem chybějících hodnot:** Pokud pod MCAR umažete nezávisle 50 % hodnot v $Y_1$ a 50 % v $Y_2$, podíl případů s oběma chybějícími hodnotami je jen $25\,\%$. Pod MAR (přes standardní pravostrannou logistickou funkci na $X_1 \sim N(0,1)$) vyjde dokonce $29\,\%$. Chcete-li dosáhnout přesně $50\,\%$ společného chybění, musíte pro MCAR spočítat $p = \sqrt{0{,}5} = 0{,}707$ (čímž vám zbude jen $8{,}6\,\%$ kompletních řádků!) a pro MAR musíte složitě numericky řešit integrály pro horizontální posun logistické křivky ($b = 0{,}922$ pro 2 proměnné, pro $\ge 3$ proměnné prakticky neřešitelné).
2. **Nezamýšlené zkreslení dat (*ceiling effect*):** Horizontální posun logistické funkce směrem k asymptotě $y=1$ způsobí, že se mnohem agresivněji umaže pravý chvost rozdělení $Y_1$. Tím se uměle zvýší šikmost pozorovaných dat a zhorší se zkreslení – takže následná metoda (např. imputace nebo test) pak může vypadat hůř či lépe jen kvůli špatně naprogramované simulaci.

### Řešení: Vícerozměrná amputace (`mice::ampute`, Sekce 3)
Autoři rozvíjejí koncept Brand (1999) a implementují jej do R funkce `mice::ampute()` v 5 krocích (Obrázek 2):

1. **Definice vzorců chybění (`patterns`):** Výzkumník zadá matici vzorců z nul a jedniček (`0` = bude smazáno, `1` = zůstane pozorováno).
2. **Rozdělení na kandidátské podmnožiny (`freq`):** Kompletní data o $n$ řádcích se náhodně rozdělí do $k$ disjunktních skupin kandidátů podle zadaných relativních četností (`freq`, součet = 1). Každý řádek je kandidátem **maximálně pro jeden** vzorec chybění.
3. **Výpočet váženého součtového skóre (`weights`):** Pro každého kandidáta $i$ v daném vzorci se spočte lineární kombinace:
   $$\text{wss}_i = w_1 y_{1i} + w_2 y_{2i} + \dots + w_m y_{mi}$$
   Tím se elegantně řídí mechanismus chybění:
   * **MCAR:** Všechny váhy jsou `0` (`mech = "MCAR"`).
   * **MAR:** Proměnné, které se v daném vzorci mažou (mají `0` v `patterns`), dostanou váhu `0`; pozorované proměnné dostanou nenulové váhy.
   * **MNAR:** Alespoň jedna proměnná, která se maže (`0` v `patterns`), dostane nenulovou váhu. Lze tak snadno tvořit i smíšené mechanismy (např. *weak MAR* nebo *weak MNAR* kombinací vah `1:5`).
4. **Převod $\text{wss}_i$ na pravděpodobnost chybění (`type`):** Standardizované skóre $\text{wss}_i$ se prožene jednou ze **4 variant logistické funkce** (Obrázek 3):
   * `RIGHT` (mažou se vysoké hodnoty $\text{wss}$),
   * `LEFT` (mažou se nízké hodnoty $\text{wss}$),
   * `MID` (mažou se průměrné hodnoty kolem 0),
   * `TAIL` (mažou se oba extrémní konce – nízké i vysoké hodnoty!).
5. **Kalibrace na přesný podíl (`prop`) a smazání:** Pravděpodobnosti se automaticky přeškálují tak, aby výsledný podíl neúplných řádků přesně odpovídal zadanému `prop`, následně proběhne losování `NA` a spojení všech podmnožin zpět do jednoho datasetu.

### Validace v simulacích (Sekce 4–5)
* **Sekce 4 (Validita `ampute`):** Na $N=1\,000$ pozorováních z $\mathcal{N}_3(\mu, \Sigma)$ s korelací $\rho \in \{0{,}1; \dots; 0{,}9\}$ a $20\,\%$ společným chyběním na $(Y_1, Y_2)$ autoři otestovali 5 scénářů (`mcar`, `mar`, `weak mar`, `mnar`, `weak mnar`) při odhadu $\mathbb{E}[Y_1]=5$ pomocí *complete-case* analýzy (CCA) a vícenásobné imputace (`mice.impute.pmm`, $m=5$). Všechny scénáře se chovaly přesně podle statistické teorie (MI pod MCAR/MAR/weak MAR odstranila zkreslení a držela $95\%$ pokrytí; pod MNAR byla zkreslená).
* **Sekce 5 (Srovnání SUA vs. `ampute`):** Při cíli $50\,\%$ společného chybění na 2 a 3 proměnných standardní SUA vyrobila jen $29\,\%$ (resp. $19\,\%$) chybění, posunutá SUA ($b=0{,}922$) sice na 2 proměnných trefila $50\,\%$, ale přehnaně zkreslila odhady CCA ($-0{,}233$ vs. $-0{,}207$) a na 3 proměnných už klesla na $38\text{–}39\,\%$. Naproti tomu **`ampute` udržela přesných $50\,\%$ i stabilní zkreslení** bez ohledu na počet amputovaných proměnných.

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Schouten, R. M., Lugtig, P., & Vink, G. (2018). Generating missing values for simulation purposes: A multivariate amputation procedure. *Journal of Statistical Computation and Simulation*, 88(15), 2909–2930. https://doi.org/10.1080/00949655.2018.1491577
* **Co článek řeší / testuje:**
  * Analyzuje nedostatky ad-hoc jednorozměrné amputace (SUA) a představuje formální algoritmus **vícerozměrné amputace** implementovaný v R jako `mice::ampute()`.
  * Ověřuje, zda `ampute()` vytváří legitimní mechanismy MCAR, MAR, weak MAR, MNAR a weak MNAR a zda lépe reguluje podíl chybění i míru zkreslení než SUA.
* **Jaké má předpoklady (algoritmus `ampute`):**
  * Vstupem je **kompletní** vícerozměrný dataset.
  * Závislost pravděpodobnosti chybění se vyjadřuje skrze **lineární vážený součet** ($\text{wss}_i = \sum w_j y_{ji}$) a jednu ze 4 hladkých logistických křivek (`RIGHT`, `LEFT`, `MID`, `TAIL`).
  * Kategorické proměnné jsou uvnitř funkce převedeny na numerické hodnoty (1, 2, 3...).
* **Na jakých datech ho autoři testovali:**
  * *Sekce 4:* Simulovaná vícerozměrná normální data ($N = 1\,000$, $p = 3$ proměnné $Y_1, Y_2, X_1$, $\mu = (5, 5, 10)^T$, jednotkové rozptyly, korelace $\rho \in \{0{,}1; \dots; 0{,}9\}$), 1 000 replikací, $20\,\%$ chybění (`RIGHT`) na vzorci `(0, 0, 1)`.
  * *Sekce 5:* Stejné DGP pro $p=3$ a $p=4$ ($Y_1, Y_2, Y_3, X_1$), $\rho \in \{0{,}2; 0{,}5; 0{,}8\}$, cíl $50\,\%$ společného chybění (`RIGHT`), 1 000 replikací.
* **Hlavní výsledky:**
  * `mice::ampute()` přesně dodržuje zadaný podíl chybějících dat (`prop`), umožňuje nezávisle nastavovat frekvence různých vzorců chybění (`freq`) a spolehlivě generuje čisté i smíšené mechanismy (MCAR, MAR, MNAR) bez umělého zkreslení chvostů známého ze SUA.
* **Jaké má slabiny a limity (Pozor na ně při psaní R kódu pro diplomku!):**
  1. **Pouze lineární skóre $\text{wss}_i$ a 4 logistické tvary:** `ampute()` umí skvěle vytvořit `RIGHT`, `LEFT`, `MID` a `TAIL`, ale **neumí přímočaře vytvořit složitější nelineární či vícenásobně pásmové mechanismy** (jako byl např. 5-pásmový symetrický MAR z článku **Yuan et al., 2018**, kde střed + oba kraje chybějí současně!). Takové scénáře v R musíte naprogramovat ručně (pár řádků kódu navíc) jako doplněk k `ampute()`.
  2. **Zacházení s nominálními (faktorovými) proměnnými (Sekce 3.2):** `ampute()` převádí kategorické proměnné na čísla (1, 2, 3...). Pokud máte v ekonomickém datasetu **neuspořádanou** proměnnou (např. kraj 1–14 nebo profesi), lineární vážený součet s těmito čísly nedává smysl (pokud si kategorie předem nepřevedete na *dummy* indikátory nebo nepoužijete vlastní amputační skript).
  3. **Testováno pouze na normálních datech a `RIGHT` variantě (přiznáno v Sekci 6.2):** Autoři sami v závěru píší, že netestovali chování `ampute()` na nenormálních (šikmých) ani víceúrovňových datech či u nelineárních vztahů. U šikmých příjmových dat může extrémní hodnota silně ovlivnit standardizaci $\text{wss}_i$ – je dobré to v R předem zkontrolovat.
  4. **Vzájemně disjunktní kandidáti vzorců:** Každý řádek v `ampute()` může být kandidátem *jen pro jeden* ze zadaných vzorců v matici `patterns`. Pokud chcete mít u $p=10$ proměnných realistickou směs kombinací chybění, výchozí nastavení `ampute` generuje jen vzorce s chybějící *jednou* proměnnou (pokud matici `patterns` sami nerozšíříte).

---

## 3. Zřetězená databáze citací (Citation Chain)

### BibTeX záznam (do Zotera / LaTeXu)
```bibtex
@article{schouten2018generating,
  title={Generating missing values for simulation purposes: a multivariate amputation procedure},
  author={Schouten, Rianne Margaretha and Lugtig, Peter and Vink, Gerko},
  journal={Journal of Statistical Computation and Simulation},
  volume={88},
  number={15},
  pages={2909--2930},
  year={2018},
  publisher={Taylor \& Francis},
  doi={10.1080/00949655.2018.1491577}
}
```

### Řetězec souvisejících prací (klíčové citace z tohoto článku)

* **Brand, J. P. L. (1999).** *Development, implementation and evaluation of multiple imputation strategies for the statistical analysis of incomplete data sets* [Disertační práce]. Erasmus University Rotterdam. (a **Brand, Van Buuren, Groothuis-Oudshoorn, & Gelsema, 2003** v *Statistica Neerlandica*, 57(1), 36–45).
  * *Role:* Původní autor myšlenky vícerozměrné amputace (nejprve realizováno jako SAS makro).
* **Van Buuren, S. (2012 / 2018).** *Flexible Imputation of Missing Data*. Chapman & Hall/CRC. (a **Van Buuren & Groothuis-Oudshoorn, 2011**, `mice` v *JSS*, 45(3), 1–67).
  * *Role:* Základní učebnice (kterou vám doporučil vedoucí na úvod), zdroj 4 logistických křivek (str. 64) a domovský R balíček `mice`, v němž je `ampute()` implementována.
* **Schouten, R., Lugtig, P., Brand, J., & Vink, G. (2018).** *Generate missing values with ampute* (Vignette balíčku `mice`).
  * *Role:* Praktická technická dokumentace všech argumentů `mice::ampute()` pro vaši implementaci v R.
* **Vink, G., Frank, L. E., Pannekoek, J., & Van Buuren, S. (2014).** Predictive mean matching imputation of semicontinuous variables. *Statistica Neerlandica*, 68(1), 61–90.
  * *Role:* Vysvětluje PMM imputaci u semikontinuálních ekonomických proměnných (typicky příjem s mnoha nulami).
* **Spohn, M.-L., Näf, J., Michel, L., & Meinshausen, N. (2025).** PKLM: A flexible MCAR test using classification. *Psychometrika*, 90(1), 280–303.
  * *Role:* Přímo přebírá `mice::ampute()` (s citací Schouten et al. 2018) pro testování síly MCAR testů!

---

## Rychlý praktický tip pro R: Jak z `mice::ampute()` vytěžit maximum
Až budete v Kroku 5 psát simulační skript v R, všimněte si zejména parametru **`type = "TAIL"`** (a `"MID"`):
* Když zavoláte `mice::ampute(data, prop = 0.3, mech = "MAR", type = "TAIL", ...)`, `ampute()` vymaže data pro extrémně nízké i extrémně vysoké hodnoty pozorovaného prediktoru. Průměr zůstane stejný, ale změní se rozptyl – takže si jedním řádkem kódu vygenerujete scénář, kde **`naniar::mcar_test()` (Little)** pohoří, zatímco **`MissMech`** a **`PKLMtest`** budou mít vysokou sílu!
* A pokud máte v datech nominální faktor (např. vzdělání), před voláním `ampute()` jej jen pro výpočet vah rozbijte na dummy proměnné, nebo pro něj definujte explicitní podmínku mimo `ampute()`.

---

Máte teď zpracovaných **všech 6 klíčových článků** (Little 1988, Molenberghs et al. 2008, Jamshidian & Jalal 2010, Rhoads 2012, Yuan et al. 2018, Berrett & Samworth 2023, Spohn et al. 2025, Schouten et al. 2018 – dohromady dokonce 7!). 

Pokud chcete, můžeme teď rovnou přejít ke **Kroku 2 a Kroku 3** z našeho plánu: připravit vám kompletní, vybroušený návrh **Tezí diplomové práce** + finální verzi **oficiálního zadání (1 odstavec + 3 citace)**, které můžete poslat vedoucímu ke schválení. Chcete ho připravit?