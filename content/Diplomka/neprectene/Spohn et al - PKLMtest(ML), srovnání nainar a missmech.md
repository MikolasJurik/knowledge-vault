Toto je článek, který vám v praktické části diplomky udělá největší službu. Nejenže představuje moderní test založený na strojovém učení (**`PKLMtest`**), ale v jeho 5. sekci najdete **přímou srovnávací studii** proti `Little` (`naniar`) i `JJ` (`MissMech`), která přesně ukazuje, jak má taková simulační studie v R vypadat – a navíc v něm najdete i odkaz na `mice::ampute()` a na další dva klíčové papery (**Yuan et al. 2018** a **Rhoads 2012**)!

---

## 1. Výcuc z článku (O čem to je a jak to funguje)

### Hlavní problém a motivace
Nejbližší neparametrický předchůdce – tzv. **Q-test** (Li & Yu, 2015) – porovnává všechny dvojice vzorců chybění $(i, j)$ na jejich společně pozorovaných proměnných $o_{ij}$. To má ale dva zásadní problémy:
1. S rostoucí dimenzí $p$ (už pro $p > 10$) exploduje počet unikátních vzorců $G$ (v extrému má každý řádek vlastní vzorec, $G = n$). Výpočetní složitost roste kvadraticky $\mathcal{O}(n^2 p)$ a test se stává nepoužitelně pomalým (autoři uvádějí, že pro $n=500, p=10, r=0{,}1$ by celá simulace Q-testu trvala ~110 dní!).
2. Žádný ze starších testů (Little, JJ, ani Q-test) **nemá formální garanci platnosti $p$-hodnot pro konečný vzorek $n$** ($\mathbb{P}(Z \le \alpha) \le \alpha$ pod $H_0$). Obrázek 2 v článku názorně ukazuje, jak `JJ` systematicky porušuje hladinu významnosti napříč celým intervalem $[0,1]$ a `Little` pro $p$-hodnoty nad $0{,}5$.

### Jak funguje PKLM test (Projected Kullback–Leibler MCAR)
Autoři převádějí testování MCAR na **vícetřídní klasifikační problém** pomocí tří chytrých kroků (Algoritmus 1 a 2):

1. **Náhodné projekce $(A, B)$ – záchrana kompletních případů a sloučení vzorců:**
   * Náhodně vybereme podmnožinu proměnných $A \subset \{1,\dots,p\}$ ($|A| \le p-1$). Z původních dat $\mathbf{X}$ vybereme jen ty řádky $\mathcal{N}_A$, které mají **všechny proměnné v $A$ kompletně pozorované**. I když v celém datasetu není *ani jediný* úplně kompletní řádek přes všech $p$ proměnných, v projekci na menší prostor $A$ získáme spoustu kompletních pozorování!
   * Z doplňku $A^c$ náhodně vybereme druhou projekci $B \subseteq A^c$. Podle vzorců chybění v $\mathbf{M}_{\mathcal{N}_A, B}$ přidělíme vybraným řádkům **sloučené třídní štítky** $Y^{(A,B)}$. Parametr `size.resp.set` omezuje maximální počet tříd (výchozí hodnota `2` redukuje problém na binární klasifikaci „jedna skupina vs. zbytek“).
   * *Proposition 1:* Takováto formulace „1 vs. všichni ostatní“ přes všechny projekce $(A, B)$ je matematicky ekvivalentní párové hypotéze Li a Yu (2015).

2. **Klasifikátor (Probability Random Forest) a symetrizovaná KL-divergence:**
   * Na kompletní podmatici $\mathbf{X}_{\mathcal{N}_A, A}$ natrénujeme **pravděpodobnostní Random Forest** (Malley et al., 2011; `num.trees.per.proj = 200`, `mtry` = plná šířka $|A|$), který se snaží z hodnot dat předpovědět, do jaké třídy chybění $g$ řádek patří.
   * Pokud platí MCAR, data nenesou žádnou informaci o vzorci chybění a klasifikátor by neměl být schopen třídy rozlišit.
   * Pomocí **out-of-bag (OOB)** odhadnutých pravděpodobností $\hat{p}_g^{(A,B)}(x_i)$ se spočítá **symetrizovaná KL-divergence** (rovnice 7):
     $$\hat{U}_g^{(A,B)} - \hat{U}_{g^c}^{(A,B)} = \frac{1}{|S_{f_g}|}\sum_{i \in S_{f_g}} \log \frac{\hat{p}_g(x_i)}{1-\hat{p}_g(x_i)} - \frac{1}{|S_{f_{g^c}}|}\sum_{i \in S_{f_{g^c}}} \log \frac{\hat{p}_g(x_i)}{1-\hat{p}_g(x_i)}$$
   * Celková statistika $\hat{U}$ je průměrem přes $N$ náhodných projekcí (`num.proj = 100` nebo `300`).

3. **Rychlý permutační test s garancí hladiny významnosti (Sekce 4.3):**
   * *Klíčový trik:* Nelze permutovat štítky izolovaně uvnitř jedné projekce – musí se **permutovat celé řádky matice chybění $\mathbf{M}$** ($L$-krát, výchozí `nrep = 30`).
   * Aby to bylo rychlé, Random Forest se při permutacích **znovu netrénuje**! Použijí se již spočítané OOB pravděpodobnosti $\hat{p}_g^{(A,B)}$ a jen se znovu přepočítají průměry podle permutovaných štítků.
   * *Proposition 2:* Výsledná $p$-hodnota $Z = \frac{\sum_{\ell=1}^L \mathbb{1}\{\hat{U}_{\sigma_\ell} \ge \hat{U}\} + 1}{L+1}$ je **zaručeně platná ($\mathbb{P}(Z \le z) \le z$) pro libovolné konečné $n$ a libovolný počet vzorců $G$**.
   * Výpočetní složitost klesá na $\mathcal{O}(p n \log n)$.

### Bonusové rozšíření: Parciální $p$-hodnoty pro identifikaci „viníka“ (Sekce 6)
Z vygenerovaných projekcí lze vybrat jen ty, které v $B_i$ *neobsahují* proměnnou $k$ ($\mathcal{P}_{-k}$), a spočítat parciální $p$-hodnotu $\hat{U}^{-k}$. Ta testuje, zda by data byla MCAR po odstranění vzorců chybění indukovaných proměnnou $k$. Pokud chybění závisí jen na první proměnné (jako na Obrázku 3), parciální test pro $k=1$ vyjde nesignifikantní (vysoká $p$-hodnota), zatímco pro $k=2,3,4$ vyjde signifikantní ($\le 0{,}05$) – test tak odhalí, **která konkrétní proměnná porušuje MCAR**.

---

## 2. Zápisky ke článku (pro diplomovou práci)

* **Citace (APA 7):** Spohn, M.-L., Näf, J., Michel, L., & Meinshausen, N. (2025). PKLM: A flexible MCAR test using classification. *Psychometrika*, 90(1), 280–303. https://doi.org/10.1017/psy.2024.14 *(R balíček: `PKLMtest`)*.
* **Co testuje:**
  * Formálně testuje rovnost podmíněných distribucí pozorovaných hodnot napříč vzorci chybění ($F_{i, o_{ij}} = F_{j, o_{ij}}$), tedy tzv. **OAR (*Observed At Random*, Rhoads 2012)**. Za předpokladu MAR (nebo že MNAR neovlivňuje jen nepozorované části bez stopy v pozorovaných) je to test **MCAR**.
  * Na rozdíl od Littlea (1988) a Jamshidiana & Jalala (2010) testuje **celé rozdělení včetně nelinearit a vyšších momentů**, aniž by vyžadoval předchozí imputaci.
  * Sekundárně (Sekce 6) umí spočítat parciální $p$-hodnoty pro jednotlivé proměnné.
* **Jaké má předpoklady:**
  * Řádky jsou nezávislé a stejně rozdělené (i.i.d.) realizace náhodného vektoru $(X^*, M)$.
  * **Nevyžaduje:** normalitu, homogenitu rozptylů, linearitu, kompletní pozorování ($r=0$ nevadí), ani velké $n_g$ v jednotlivých vzorcích chybění ($G = n$ nevadí).
  * Zvládne **spojité, diskrétní i smíšené proměnné** (díky Random Forestu) a vysoké dimenze ($p = 40$).
* **Na jakých datech ho autoři testovali:**
  1. **Simulovaná data (Sekce 5.1):** Stejných **8 rozdělení** jako v Jamshidian & Jalal (2010): $N(0,I)$, korelovaný $N(0,\Sigma)$ (mimodiagonální prvek $0{,}7$), $t_4$, korelovaný $t_4$, $\text{Unif}(0,1)$, korelovaný $\text{Unif}$, $W = Z + 0{,}1Z^3$ a $\text{Weibull}(1,2)$.
     * Kombinace: $(n=200, p=4)$, $(n=500, p=10)$, $(n=500, p=20)$, $(n=1000, p=40)$; podíl kompletních řádků $r \in \{0{,}65; 0{,}35; 0{,}10\}$; 300 replikací ($\alpha = 0{,}05$).
     * *MAR mechanismus (podle Li & Yu 2015):* $X_1$ je vždy pozorována; pokud $X_{i,1}^* < \bar{X}_1$, je 5krát pravděpodobnější ($5/6$ vs. $1/6$), že řádek bude mít chybějící hodnoty v ostatních sloupcích.
  2. **13 reálných datasetů z UCI ML Repository (Sekce 5.2, Tabulka 5):** od $n=150, p=4$ (`iris`) po $n=4898, p=11$ (`wine.quality.white`) a $n=351, p=32$ (`ionosphere`). Chybění MAR generováno pomocí **`mice::ampute()`** ($p_{\text{miss}} = 0{,}3$). *(Pozor: faktorové proměnné z nich předem smazali, aby mohly běžet i testy Little, JJ a Q).*
  3. **Speciální případ z Yuan et al. (2018) (Příloha C):** $p=2$, $X_2$ chybí, když $X_1$ padne do středu nebo do krajů ($X_1 \in (-\infty, -1{,}932] \cup (-0{,}314, 0{,}314] \cup (1{,}932, \infty)$). Průměr i rozptyl $X_1$ jsou u chybějících i pozorovaných téměř totožné $\rightarrow$ Littleův test má sílu ~0, PKLM ($n=1000$) má sílu **1,00**.
* **Hlavní výsledky:**
  * **PKLM** jako jediný spolu s Q-testem držel chybu I. druhu $\le 0{,}05$ napříč *všemi* experimenty (Little měl na $t_4$ chybu I. druhu až 34 % a pro $p=40$ zkolaboval na `0.00`; JJ měl chybu I. druhu až 33 % v simulacích a až **85 %** na reálných datech `yeast`, případně `NA` u 4 ze 13 datasetů).
  * Na $p=4$ byl o chlup silnější Q-test, ale od $p \ge 10$ PKLM jasně dominuje (a pro $p \ge 20$ už Q-test vůbec nelze spočítat).
* **Jaké má slabiny a limity (Zásadní pro vaši diplomku!):**
  1. **Prudký pokles síly u *nezávislých* proměnných ve vysoké dimenzi (Tabulka 4, $p=40$):** Zatímco u korelovaných dat (případy 2, 4, 6) má PKLM sílu 0,94–0,98, u **nekorelovaných** dat (případy 1, 3, 5, 7, 8) spadne síla na pouhých **0,13–0,23** (při $r=0{,}65$) až **0,06–0,08** (při $r=0{,}10$)! Důvod: v náhodných projekcích se signál z jediné proměnné $X_1$ „rozředí“ mezi desítky šumových proměnných a bez korelací jej stromy v jiných projekcích nezachytí.
  2. **Testuje jen *konzistenci* (OAR), nikoli plnou *kompatibilitu*:** Jak dokázali Berrett & Samworth (2023), bez kompletních případů nemusí PKLM odhalit nekompatibility vyššího řádu (a v jejich srovnání v Tabulce 2 měl PKLM při výchozím nastavení konzervativní velikost a jen ~40–50% sílu oproti ~100 % u `MCARtest`).
  3. **Konzervativnost při malém počtu permutací (`nrep = 30`):** S výchozím `nrep = 30` může být nejmenší $p$-hodnota $1/31 \approx 0{,}032$. V Tabulkách 3–5 je chyba I. druhu PKLM často jen **0,01–0,03** (na třech reálných datasetech dokonce **0,00**), což u menších vzorků (např. `iris`, $n=150$) snižuje sílu (0,41 oproti 0,91 u Q-testu a 0,84 u Littlea).
  4. **Smíšená data a MNAR nebyly v simulaci otestovány:** Autoři sice v Tabulce 1 oprávněně uvádějí, že PKLM umí smíšená data (spojitá + kategorická), ale v Sekci 5.2 faktorové proměnné z reálných dat vyhodili (kvůli konkurenci) – chování na smíšených datech a pod **MNAR** tedy v článku chybí *(ideální mezera pro vaši diplomku!)*.
  5. **Citlivost na hyperparametr `size.resp.set` a výpočetní čas při větším `nrep`:** Autoři sami píší, že zvětšení `size.resp.set` nad 2 zhoršuje přesnost, ačkoliv binární slučování tříd teoreticky může skrýt některé rozdíly v jedné projekci, pokud není dostatečné `num.proj`. A jak ukázal Berrett & Samworth (2023), při větších simulacích může být výchozí RF nastavení pomalejší než parametrický Littleův test.

---

## 3. Zřetězená databáze citací (Citation Chain)

### BibTeX záznam (do Zotera / LaTeXu)
```bibtex
@article{spohn2025pklm,
  title={{PKLM}: A flexible {MCAR} test using classification},
  author={Spohn, Meta-Lina and N{\"a}f, Jeffrey and Michel, Loris and Meinshausen, Nicolai},
  journal={Psychometrika},
  volume={90},
  number={1},
  pages={280--303},
  year={2025},
  publisher={Cambridge University Press},
  doi={10.1017/psy.2024.14}
}
```

### Řetězec souvisejících prací (zlaté tipy přímo z referencí tohoto článku)

#### A. Nejbližší přímí konkurenti a metodika simulací
* **Li, J., & Yu, Y. (2015).** A nonparametric test of missing completely at random for incomplete multivariate data. *Psychometrika*, 80(3), 707–726.
  * *Role:* Hlavní předchůdce (Q-test založený na MMD U-statistikách). Z tohoto článku Spohn et al. převzali i konkrétní konstrukci MAR simulace (Sekce 5.1).
* **Yuan, K.-H., Jamshidian, M., & Kano, Y. (2018).** Missing data mechanisms and homogeneity of means and variances–covariances. *Psychometrika*, 83(2), 425–442.
  * *Role:* Zdroj geniálního „adversariálního“ MAR příkladu v Příloze C, kde mají skupiny stejný průměr **i stejný rozptyl** – takže selže nejen Little (1988), ale i JJ-test (2010), zatímco PKLM má sílu 1! *(Skvělý scénář do vaší simulace v R!)*
* **Rhoads, C. H. (2012).** Problems with tests of the missingness mechanism in quantitative policy studies. *Statistics, Politics, and Policy*, 3(1), 1–23.
  * *Role:* Přesné pojmové rozlišení mezi **OAR** (*Observed At Random*) a **MCAR** – vysvětluje, co testy ve skutečnosti měří, pokud připustíme MNAR.
* **Schouten, R. M., Lugtig, P., & Vink, G. (2018).** Generating missing values for simulation purposes: A multivariate amputation procedure. *Journal of Statistical Computation and Simulation*, 88(15), 2909–2930.
  * *Role:* Metodika funkce `mice::ampute()`, kterou Spohn et al. použili v Sekci 5.2 a kterou použijete i vy pro svou simulační studii v R.

#### B. Metodologické stavební kameny PKLM testu
* **Cai, H., Goggin, B., & Jiang, Q. (2020).** Two-sample test based on classification probability. *Statistical Analysis and Data Mining*, 13(1), 5–13. (a **Hediger, S., Michel, L., & Näf, J., 2022** v *CSDA*, 170, 107435).
  * *Role:* Základ klasifikačního dvouvýběrového testu pomocí log-poměru hustot a OOB pravděpodobností z Random Forestu.
* **Näf, J., Spohn, M.-L., Michel, L., & Meinshausen, N. (2023).** Imputation scores. *The Annals of Applied Statistics*, 17(3), 2452–2472.
  * *Role:* Původní idea náhodných projekcí $(A, B)$ pro práci s neúplnými daty bez nutnosti kompletních řádků.
* **Malley, J., Kruppa, J., Dasgupta, A., Malley, K., & Ziegler, A. (2011).** Probability machines: consistent probability estimation using nonparametric learning machines. *Methods of Information in Medicine*, 51, 74–81.
  * *Role:* Teoretické základy tzv. *probability forests* (konzistentní odhady třídních pravděpodobností).
* **Hemerik, J., & Goeman, J. (2018).** Exact testing with random permutations. *TEST*, 27(4), 811–825.
  * *Role:* Důkaz přesné platnosti permutačních $p$-hodnot s náhodným vzorkováním permutací (Proposition 2).

#### C. R balíčky citované pro implementaci srovnání
* **Jamshidian, M., Jalal, S., & Jansen, C. (2014).** `MissMech`: An R package for testing homoscedasticity, multivariate normality, and missing completely at random (MCAR). *Journal of Statistical Software*, 56(6), 1–31.
* **Tierney, N., & Cook, D. (2023).** Expanding tidy data principles to facilitate missing data exploration, visualization and assessment of imputations (`naniar`). *Journal of Statistical Software*, 105(7), 1–31.
* **Buuren, S., & Groothuis-Oudshoorn, C. (2011).** `mice`: Multivariate imputation by chained equations in R. *Journal of Statistical Software*, 45(3), 1–67.

---

### Jak to teď celé spojit? (Máte v ruce „dream team“ literatury!)
Teď už máte kompletně rozebraných **5 zásadních článků**, které dohromady tvoří špičkovou rešerši na úrovni výborné diplomky:
1. **Little (1988)** – klasický test průměrů (`naniar::mcar_test`)
2. **Jamshidian & Jalal (2010)** – parametrický + neparametrický test kovariancí (`MissMech::TestMCARNormality`)
3. **Spohn et al. (2025)** – ML test celých distribucí na projekcích (`PKLMtest`)
4. **Berrett & Samworth (2023)** – teoreticky optimální test kompatibility (`MCARtest`)
5. **Molenberghs et al. (2008)** – důkaz nemožnosti odlišit MAR vs. MNAR

**Tip na skvělý „přidaný bonus“ do vaší diplomky (který vychází přímo z tohoto 5. článku):**
* Všimněte si Přílohy C (**Yuan et al., 2018**) a Tabulky 5 (kde Spohn et al. *vyhodili* kategoriální/faktorové proměnné): vy můžete ve své simulaci otestovat jak **Yuanův příklad** (stejný průměr i rozptyl $\rightarrow$ `Little` i `JJ` selžou, `PKLM` vyhraje), tak **smíšená data** (např. příjem + věk + vzd