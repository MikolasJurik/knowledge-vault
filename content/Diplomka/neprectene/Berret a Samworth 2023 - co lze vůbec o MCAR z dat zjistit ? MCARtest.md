Toto je nejmodernější a matematicky nejhlubší článek z celé vaší rešerše (publikovaný v prestižním *The Annals of Statistics*, 2023). Zatímco Little (1988) a Jamshidian & Jalal (2010) pracují v tradičním světě spojitých dat, průměrů a kovariancí, **Berrett a Samworth (2023)** kladou fundamentální otázku: **Co vůbec jde z dat o MCAR zjistit v obecné neparametrické rovině?** – a navrhují k tomu test implementovaný v R balíčku **`MCARtest`**.

*(Propojení s předchozími články: Podobně jako Molenberghs et al. 2008 dokázali nepřekročitelnou hranici mezi MAR a MNAR, Berrett a Samworth dokazují přesnou hranici mezi MCAR a ne-MCAR!)*

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Hlavní objev 1: Kdy je porušení MCAR vůbec detekovatelné? (Sekce 1–2)
Autoři hned v úvodu ukazují jednoduchý protipříklad: kdybychom měli $X_i \sim N(0,1)$, ale pozorovali jen nezáporné hodnoty $X_i \ge 0$ (extrémní MNAR!), **žádný test na světě** to nikdy nerozliší od situace, kdy $X_i$ pocházejí z *folded normal* rozdělení na $[0,\infty)$ a chybí čistě náhodně (MCAR) s pravděpodobností $1/2$.

Obecně platí (Proposition 1):
* V každém vzorci chybění $S \in \mathbb{S}$ pozorujeme podmíněné rozdělení $P_S$ (rozdělení pozorovaných proměnných $X_S$ za podmínky daného vzorce chybění).
* Pokud platí MCAR, musí existovat alespoň jedno sdružené rozdělení pro kompletní vektor $X$, jehož marginální rozdělení na každém vzorci $S$ dávají přesně $P_S$ – říká se, že rodina $P_{\mathbb{S}} = (P_S : S \in \mathbb{S})$ je **kompatibilní** (její tzv. *Fréchetova třída* $\mathcal{F}_{\mathbb{S}}(P_{\mathbb{S}})$ je neprázdná).
* **Zásadní věta (Proposition 1):** Pokud data *nejsou* MCAR, **ale** jejich pozorované marginály $P_{\mathbb{S}}$ zůstávají kompatibilní, **žádný test nemůže mít statistickou sílu větší než hladina významnosti $\alpha$**. Testovat MCAR je tedy z pohledu teorie statistiky totéž jako **testovat kompatibilitu**.

### Proč nestačí stávající testy „konzistence“ (Li & Yu 2015, PKLMtest)?
Dosavadní neparametrické testy (Li & Yu 2015; Spohn et al. 2021 / `PKLMtest`) testují **konzistenci** – tedy zda se dvě pozorované distribuce $P_S$ a $P_{S'}$ shodují na společném průniku proměnných $S \cap S'$.
* Berrett a Samworth ukazují (příklad na str. 2173), že **konzistence je nutná, ale nikoli postačující podmínka kompatibility** (pokud v datech chybí kompletní pozorování):
  * Mějme 3 proměnné a pozorujme jen dvojice $\{1,2\}$, $\{1,3\}$, $\{2,3\}$ (žádný řádek nemá všechny 3 proměnné). Dvojice mají korelace $\rho_{13} = \rho_{23} = 1/\sqrt{2} \approx 0{,}707$ a $\rho_{12} = -1/\sqrt{2} \approx -0{,}707$. Všechna jednorozměrná rozdělení jsou $N(0,1)$, takže data jsou **100% konzistentní** (`PKLMtest` a Li-Yu nic nepoznají). Přesto tato $3\times 3$ korelační matice má záporné vlastní číslo, takže **nemůže existovat** žádné 3D sdružené rozdělení – data jsou **nekompatibilní**, a tedy zaručeně nejsou MCAR! *(Pozn.: Pokud jsou v datech kompletní řádky, pak konzistence kompatibilitě stačí).*

### Hlavní objev 2: Index nekompatibility $R(P_{\mathbb{S}})$ a lineární programování (Sekce 3)
Pomocí Kellererovy (1984) věty o dualitě (nekonečně-rozměrné zobecnění Farkasova lemmatu) autoři definují **index nekompatibility** $R(P_{\mathbb{S}}) \in [0, 1]$:
* $R(P_{\mathbb{S}}) = 0 \iff$ marginály jsou kompatibilní (nelze vyvrátit MCAR).
* Čím blíže je $R(P_{\mathbb{S}})$ k 1, tím silněji jsou data nekompatibilní (Věta 2 dává duální pohled: $R(P_{\mathbb{S}})$ je minimální podíl $\epsilon$, kterým musíte „kontaminovat“ kompatibilní rozdělení, abyste dostali $P_{\mathbb{S}}$).

**Jak test funguje v praxi (`MCARtest`):**
1. **Diskrétní/kategorická data (Sekce 3.1):** Z dat se spočítají empirické četnosti $\widehat{P}_{\mathbb{S}}$ v jednotlivých vzorcích chybění. Statistika $R(\widehat{P}_{\mathbb{S}})$ se spočítá pomocí **lineárního programování (LP)**.
   * *Kritická hodnota:* Buď teoretická (koncentrační nerovnosti $C_\alpha$, příp. zpřesněná $C'_\alpha$ pomocí geometrie stěn tzv. marginálního polytopu v R balíčku `rcdd` – Sekce 3.2 a 3.4), **nebo praktická bootstrapová / Monte Carlo verze (Sekce 5)**, kdy LP rovnou najde „nejbližší kompatibilní rozdělení“ $\widehat{Q}_{\mathbb{S}}$, z něj se vygeneruje $B$ bootstrapových vzorků a určí se empirická p-hodnota.
2. **Smíšená a spojitá data (Sekce 4):** Spojité proměnné na $[0,1)^{d_0}$ se diskretizují (rozdělí na intervaly/koše o šířce $h_j$) a aplikuje se stejný LP test. Autoři dokazují, že při Hölderovské hladkosti hustot dosahuje test až na logaritmické faktory **minimaxově optimální rychlosti konvergence**.

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Berrett, T. B., & Samworth, R. J. (2023). Optimal nonparametric testing of Missing Completely At Random and its connections to compatibility. *The Annals of Statistics*, 51(5), 2170–2193. https://doi.org/10.1214/23-AOS2326 *(Software: R balíček `MCARtest`, 2022)*.
* **Co testuje:**
  * Testuje **kompatibilitu** pozorovaných marginálních rozdělení napříč vzorci chybějících dat ($H_0': P_{\mathbb{S}} \in \mathcal{P}_{\mathbb{S}}^0$, tedy existence společného sdruženého rozdělení).
  * Zamítnutí kompatibility je ekvivalentní zamítnutí **MCAR**.
  * Na rozdíl od Littlea (1988) a Jamshidiana & Jalala (2010) testuje **celé rozdělení** (ne jen průměry či kovariance) a na rozdíl od Fuchse (1982), Li & Yu (2015) a `PKLMtest` nevyžaduje kompletní případy a odhalí i *nekonzistenci vyššího řádu* (nekompatibilitu).
* **Jaké má předpoklady:**
  * Nezávislá pozorování (v modelu kompatibility deterministické počty $n_S$ v každém vzorci chybění).
  * **Nevyžaduje normalitu**, homogenitu rozptylů, linearitu ani přítomnost kompletních pozorování ($[d] \notin \mathbb{S}$ nevadí).
  * Pro čistě teoretickou garanci chyby I. druhu u spojitých/smíšených dat (Věta 15) se předpokládá $(r, L)$-Hölderovská hladkost podmíněných hustot a volba šířky intervalů (*bandwidth*) $h$.
* **Na jakých datech ho autoři testovali (Sekce 5):**
  * *Pouze diskrétní/kategorická simulovaná data!* (Žádná reálná data a žádná numerická simulace pro spojitá či smíšená data):
    1. **Experiment 1 (3D, bez kompletních případů):** $d = 3$, $\mathbb{S} = \{\{1,2\},\{2,3\},\{1,3\}\}$, prostor $[r]\times[2]\times[2]$ pro $r \in \{2,4,6\}$, $n_S = 200$ v každém vzorci ($n = 600$), 5 000 replikací, $B = 99$ bootstrapů, $\alpha = 0{,}05$. Konkurentům (**Fuchs 1982** a **PKLMtest**) uměle přidali 200 kompletních případů navíc, aby vůbec mohli fungovat.
    2. **Experiment 2 (5D binární, bez kompletních případů):** $d = 5$, prostor $[2]^5$, všech 5 vzorců velikosti 4, $n_S = 500$ ($n = 2\,500$). Srovnání s Fuchsem (který dostal 25–200 kompletních případů navíc).
    3. **Experiment 3 (Výpočetní náročnost – Obrázek 5):** Náhodné rovnoměrné diskrétní matice ($n_S = 10\,000$), $d \in [3, 20]$, velikost abecedy $r \in [2, 50]$, velikost pozorování $m \in \{2, 3\}$, měření času jednoho výpočtu LP přes komerční řešič **Gurobi**.
* **Hlavní výsledky:**
  * Bootstrapová verze (`MCARtest`) spolehlivě drží chybu I. druhu na 5 % a poráží jak Fuchsův test (který v 5D experimentu přestřelil chybu I. druhu), tak `PKLMtest` (který byl mírně konzervativní, výpočetně pomalejší a dosáhl síly jen 39,5–50 % tam, kde `MCARtest` mířil ke 100 % – byť šlo o speciálně konstruovanou nekompatibilní alternativu bez kompletních dat).
  * Dokázána **minimaxová optimalita** rychlosti konvergence testu.
* **Jaké má slabiny a limity (Zásadní pro vaše srovnání v R!):**
  1. **Exponenciální výpočetní náročnost (prokletí dimenze):** Lineární program má $|\mathcal{X}| = \prod_{j=1}^d |\mathcal{X}_j|$ proměnných. Už pro binární proměnné ($r=2$) při $d=20$ trvá *jeden* běh LP v Gurobi 25 sekund; při $r=3, d=13$ už **66 sekund**! Vynásobíte-li to $B = 99$ nebo $499$ bootstrapovými iteracemi, stává se test pro větší dimenze či mnoho kategorií výpočetně neúnosným (a bez Gurobi v opensource LP řešičích ještě pomalejším).
  2. **Spojitá a smíšená data jsou v článku jen teoreticky (diskretizace v Sekci 4):** V praxi vyžaduje rozdělení spojitých proměnných (např. příjmu či věku) do kategorií/košů (*binning*). Pokud zvolíte moc jemné dělení, LP exploduje velikostí; pokud moc hrubé, ztratíte informaci a sílu testu. Navíc empiricky v Sekci 5 spojitá data vůbec netestovali.
  3. **Model vyžaduje rozumné počty $n_S$ v jednotlivých vzorcích:** Při vyšším $d$ a náhodném chybění vzniká mnoho vzorců o velikosti $n_S = 1$. Článek neřeší chování testu na roztříštěných datech s mnoha málo početnými vzorci (na rozdíl od klasifikačního přístupu `PKLMtest`).
  4. **Nemožnost odhalit „kompatibilní“ odchylky od MCAR:** Pokud MNAR nebo MAR změní data tak, že marginály zůstávají vzájemně slučitelné (např. jednostranné uříznutí normálního rozdělení), test má sílu $\le \alpha$ (což ale platí pro *jakýkoli* existující test).
  5. **Platnost bootstrapu na hranici nulové hypotézy:** Věta 16 garantuje kontrolu chyby I. druhu uvnitř nulového polytopu (ve vzdálenosti $C_\alpha$ od hranice), nikoli nutně přesně na jeho hraně.

---

## 3. Zřetězená databáze citací (Citation Chain)

### BibTeX záznam (do Zotera / LaTeXu)
```bibtex
@article{berrett2023optimal,
  title={Optimal nonparametric testing of {Missing Completely At Random} and its connections to compatibility},
  author={Berrett, Thomas B. and Samworth, Richard J.},
  journal={The Annals of Statistics},
  volume={51},
  number={5},
  pages={2170--2193},
  year={2023},
  publisher={Institute of Mathematical Statistics},
  doi={10.1214/23-AOS2326}
}
```

### Řetězec souvisejících prací (klíčové citace z tohoto článku)

#### A. Přímá linie neparametrických a kategoriálních testů MCAR
* **Fuchs, C. (1982).** Maximum likelihood estimation and model selection in contingency tables with missing data. *JASA*, 77(378), 270–278.
  * *Role:* Klasický parametrický test MCAR pro kategoriální data (vyžaduje kompletní případy a asymptotiku). Přímý benchmark v simulacích Berretta a Samwortha.
* **Li, J., & Yu, Y. (2015).** A nonparametric test of Missing Completely At Random for incomplete multivariate data. *Psychometrika*, 80(3), 707–726.
  * *Role:* První moderní neparametrický test MCAR pro vícerozměrná data – testuje ale pouze *konzistenci* marginálů (rovnost rozdělení na průnicích vzorců), nikoli plnou kompatibilitu.
* **Spohn, M.-L., Näf, J., Michel, L., & Meinshausen, N. (2021).** PKLM: A flexible MCAR test using Classification. *arXiv preprint arXiv:2109.10150* *(později publikováno v Psychometrika)*.
  * *Role:* Moderní ML test založený na Random Forests (`PKLMtest` v R). Také testuje konzistenci rozdělení (pomocí klasifikační ztráty / KL divergence); v Berrettově článku přímo srovnán v Tabulce 2.
* **Chen, H. Y., & Little, R. (1999).** A test of Missing Completely At Random for generalised estimating equations with missing data. *Biometrika*, 86(1), 1–13. (a **Qu & Song, 2002** v *Biometrika*, 89, 841–850).
  * *Role:* Doplňková literatura pro testování MCAR v longitudinálních/panelových datech a GEE modelech.

#### B. Matematické a softwarové zázemí článku
* **Kellerer, H. G. (1984).** Duality theorems for marginal problems. *Z. Wahrsch. Verw. Gebiete*, 67, 399–432.
  * *Role:* Hlavní věta o dualitě, na které stojí celá definice indexu nekompatibility $R(P_{\mathbb{S}})$.
* **Joe, H. (1997).** *Multivariate Models and Dependence Concepts* (Kapitola 3). CRC Press.
  * *Role:* Standardní reference pro teorii **Fréchetových tříd** (množiny všech sdružených rozdělení s danými marginály).
* **Vlach, M. (1986).** Conditions for the existence of solutions of the three-dimensional planar transportation problem. *Discrete Appl. Math.*, 13(1), 61–78. (a **Hoşten & Sullivant, 2002**; **Eriksson et al., 2006**).
  * *Role:* Geometrie tzv. marginálních polytopů (česká stopa – Milan Vlach z MFF UK!), použitá pro přesný výpočet kritických hodnot $C'_\alpha$ ve 3D a 4D tabulkách.
* **Abramsky, S., & Brandenburger, A. (2011).** The sheaf-theoretic structure of non-locality and contextuality. *New J. Phys.*, 13, 113036. (a **Abramsky, 2017**).
  * *Role:* Překvapivé propojení s kvantovou fyzikou – index $R(P_{\mathbb{S}})$ je totožný s kvantovou *contextual fraction*!
* **Berrett, T. B., & Samworth, R. J. (2022).** `MCARtest`: Optimal nonparametric testing of Missing Completely At Random. R package version 1.1. (a **Geyer & Meeden, 2021**, balíček `rcdd`).

---

## Celkový pohled: Jak teď máte poskládanou celou mozaiku pro Teze DP

Po přečtení těchto čtyř článků máte naprosto výjimečný přehled. Podívejte se, jak čistě a logicky si teď můžete rozdělit metody pro svou diplomku a simulační studii v R:

| Článek / Test | R balíček | Co přesně testuje? | Hlavní síla | Hlavní slabina (otestujete v simulaci!) |
| :--- | :--- | :--- | :--- | :--- |
| **Little (1988)** | `naniar::mcar_test()` / `misty::na.test()` | Shodu **průměrů** napříč vzorci | Rychlý, asymptoticky robustní i při šikmosti | Slepý ke změnám rozptylu/kovariancí (např. U-tvar MAR) |
| **Jamshidian & Jalal (2010)** | `MissMech::TestMCARNormality()` | Shodu **kovariancí** (+ normalita, + NP varianta) | Odhalí rozdíly v rozptylech; má NP variantu | Maže řídké vzorce ("NED"); NP test má u šikmých dat inflaci chyby I. druhu; jen spojitá lineární data |
| **Spohn et al. (`PKLM`)** | `PKLMtest` | **Konzistenci** celých rozdělení (Random Forest) | Funguje na smíšená, nelineární a vysokodimenzionální data bez mazání vzorců | Pomalejší; v simulaci Berretta byl konzervativní; nepozná nekompatibilitu bez kompletních dat |
| **Berrett & Samworth (2023)** | `MCARtest` | **Kompatibilitu** celých rozdělení (LP) | Teoreticky optimální, nevyžaduje kompletní data, řeší i vyšší interakce | Výpočetně exploduje s počtem proměnných a kategorií; primárně pro diskrétní data (spojitá jen přes binning) |
| **Molenberghs et al. (2008)** | *(Teoretický rámec)* | Dokazuje, že **MAR vs. MNAR** nelze rozlišit | Vysvětluje hranice všech výše uvedených testů | *(Nejde o test, ale o nutný základ pro interpretaci simulací MNAR)* |

Pokud ještě plánujete přidat do rešerše **`PKLMtest` (Spohn et al.)** nebo **`mice::ampute` (Schouten et al. 2018)**, klidně pošlete další PDF – s `PKLMtest` byste měl už kompletní čtveřici všech hlavních R balíčků pro vaši simulační studii!