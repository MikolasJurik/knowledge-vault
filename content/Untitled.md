# Univariátní modely časových řad: Komplexní studijní text

Tags: #time-series #econometrics #statistics #mathematics #autoregression #arima #stochastic-processes

---

## Přehled kurzu a studijní mapa

Tento text představuje ucelený kurz univariátního modelování časových řad v diskrétním čase. Postupuje od základních axiomů stochastických procesů přes Woldovu dekompozici, modely autoregrese a klouzavých průměrů až po teorii integrovaných procesů a rigorózní Box-Jenkinsovu metodologii.

```
[Přednáška 1: Základní aparát & Stacionarita]
                      │
[Přednáška 2: Autoregresní modely AR(p) & PACF]
                      │
[Přednáška 3: Modely klouzavých průměrů MA(q) & Invertibilita]
                      │
[Přednáška 4: Smíšené procesy ARMA(p,q) & Woldova dekompozice]
                      │
[Přednáška 5: Nestacionarita, Jednotkový kořen & ARIMA(p,d,q)]
                      │
[Přednáška 6: Box-Jenkinsova metodologie, Odhad & Diagnostika]
```

---

# Přednáška 1: Základní aparát stochastických procesů a stacionarita

## 1.1 Formální definice stochastického procesu

Nechť $(\Omega, \mathcal{F}, P)$ je pravděpodobnostní prostor, kde:
* $\Omega$ je prostor elementárních jevů,
* $\mathcal{F}$ je $\sigma$-algebra na $\Omega$,
* $P: \mathcal{F} \to [0, 1]$ je pravděpodobnostní míra.

> [!definition] Stochastický proces v diskrétním čase
> **Stochastický proces v diskrétním čase** je parametrizovaný soubor reálných náhodných veličin definovaných na tomtéž pravděpodobnostním prostoru $(\Omega, \mathcal{F}, P)$:
> $$\{X_t\}_{t \in \mathcal{T}}, \quad \mathcal{T} \subseteq \mathbb{Z}$$
> Pro pevné $\omega \in \Omega$ se zobrazení $t \mapsto X_t(\omega)$ nazývá **realizace**, **trajektorie** nebo **časová řada**. Pro pevné $t \in \mathcal{T}$ je $X_t(\cdot)$ náhodná veličina.

V dalším textu budeme uvažovat $\mathcal{T} = \mathbb{Z} = \{\dots, -2, -1, 0, 1, 2, \dots\}$. Předpokládáme, že náhodné veličiny mají konečné druhé momenty: $X_t \in L^2(\Omega, \mathcal{F}, P)$, tedy $E[X_t^2] < \infty$ pro všechna $t \in \mathbb{Z}$.

---

## 1.2 Striktní vs. Slabá (Kovarianční) stacionarita

> [!definition] Striktní (silná) stacionarita
> Proces $\{X_t\}_{t \in \mathbb{Z}}$ je **striktně stacionární**, jestliže pro každou konečnou množinu časových indexů $\{t_1, t_2, \dots, t_n\} \subset \mathbb{Z}$ a pro každé celé číslo $h \in \mathbb{Z}$ je sdružené rozdělení pravděpodobnosti náhodného vektoru $(X_{t_1}, X_{t_2}, \dots, X_{t_n})'$ identické se sdruženým rozdělením vektoru $(X_{t_1+h}, X_{t_2+h}, \dots, X_{t_n+h})'$. Formálně zapsáno pomocí distribučních funkcí:
> $$F_{t_1, \dots, t_n}(x_1, \dots, x_n) = F_{t_1+h, \dots, t_n+h}(x_1, \dots, x_n) \quad \forall n \in \mathbb{N}, \; \forall h \in \mathbb{Z}, \; \forall x_i \in \mathbb{R}$$

Striktní stacionarita je v empirickém výzkumu téměř neověřitelná, neboť vyžaduje invarianci všech konečněrozměrných rozdělení vůči časovému posunu. Proto se v lineární teorii časových řad pracuje s konceptem opírajícím se výhradně o první dva momenty.

> [!definition] Slabá (kovarianční, momentová) stacionarita
> Proces $\{X_t\}_{t \in \mathbb{Z}}$ s konečnými druhými momenty ($E[X_t^2] < \infty$) nazýváme **slabě stacionární** (či stacionární v širším smyslu), jestliže:
> 1. **Střední hodnota je konstantní v čase:**
>    $$E[X_t] = \mu \in \mathbb{R}, \quad \forall t \in \mathbb{Z}$$
> 2. **Autokovariance mezi libovolnými dvěma okamžiky závisí pouze na jejich časové vzdálenosti (zpoždění $k$), nikoliv na čase $t$:**
>    $$Cov(X_t, X_{t-k}) = E\big[(X_t - \mu)(X_{t-k} - \mu)\big] = \gamma(k) \equiv \gamma_k, \quad \forall t, k \in \mathbb{Z}$$

Z podmínky (2) pro $k = 0$ okamžitě plyne invariance rozptylu:
$$Var(X_t) = Cov(X_t, X_t) = \gamma(0) \equiv \gamma_0 = \text{konst.} \ge 0, \quad \forall t \in \mathbb{Z}$$

### Vztah mezi striktní a slabou stacionaritou
1. **Striktní stacionarita $\implies$ Slabá stacionarita:** Platí pouze za předpokladu, že proces má konečné druhé momenty ($E[X_t^2] < \infty$). Pokud má striktně stacionární proces např. Cauchyho rozdělení (u kterého neexistuje ani první moment), *není* slabě stacionární.
2. **Slabá stacionarita $\implies$ Striktní stacionarita:** Obecně *neplatí*.
3. **Výjimka – Gaussovské procesy:** Pokud je proces $\{X_t\}$ Gaussovský (všechna konečněrozměrná rozdělení jsou vícerozměrná normální), jsou tato rozdělení jednoznačně určena svými prvními dvěma momenty (vektorem středních hodnot a kovarianční maticí). U Gaussovských procesů je tedy **slabá stacionarita ekvivalentní striktní stacionaritě**.

---

## 1.3 Autokovarianční a autokorelační funkce (ACVF a ACF)

> [!definition] Autokovarianční funkce (ACVF)
> Pro slabě stacionární proces $\{X_t\}$ je **autokovarianční funkce** (Autocovariance Function, ACVF) definována jako zobrazení $\gamma: \mathbb{Z} \to \mathbb{R}$:
> $$\gamma_k = Cov(X_t, X_{t-k}) = E\big[(X_t - \mu)(X_{t-k} - \mu)\big]$$

> [!definition] Autokorelační funkce (ACF)
> **Autokorelační funkce** (Autocorrelation Function, ACF) slabě stacionárního procesu je normalizovaná autokovariance $\rho: \mathbb{Z} \to [-1, 1]$:
> $$\rho_k = \frac{Cov(X_t, X_{t-k})}{\sqrt{Var(X_t)Var(X_{t-k})}} = \frac{\gamma_k}{\sqrt{\gamma_0 \cdot \gamma_0}} = \frac{\gamma_k}{\gamma_0}$$

### Fundamentální vlastnosti ACVF a ACF

> [!theorem] Vlastnosti $\gamma_k$ a $\rho_k$
> Nechť $\{X_t\}$ je slabě stacionární proces. Pak platí:
> 1. $\gamma_0 = Var(X_t) \ge 0$, $\rho_0 = 1$.
> 2. **Symetrie (sudost):** $\gamma_k = \gamma_{-k}$ a $\rho_k = \rho_{-k}$ pro všechna $k \in \mathbb{Z}$.
> 3. **Omezenost:** $|\gamma_k| \le \gamma_0$ a $|\rho_k| \le 1$ pro všechna $k \in \mathbb{Z}$.
> 4. **Pozitivní semidefinitnost:** Pro libovolné $n \in \mathbb{N}$, libovolné vektory $(c_1, \dots, c_n)' \in \mathbb{R}^n$ a časy $t_1, \dots, t_n \in \mathbb{Z}$ platí:
>    $$\sum_{i=1}^n \sum_{j=1}^n c_i c_j \gamma(t_i - t_j) \ge 0$$

> [!proof] Důkaz vlastností 2 a 3
> **Důkaz symetrie:**
> Z definice kovariance a komutativity násobení na $\mathbb{R}$:
> $$\gamma_{-k} = Cov(X_t, X_{t - (-k)}) = Cov(X_t, X_{t+k})$$
> Protože proces je slabě stacionární, kovariance závisí pouze na časovém posunu. Posuňme oba indexy o $-k$:
> $$Cov(X_t, X_{t+k}) = Cov(X_{t-k}, X_{(t+k)-k}) = Cov(X_{t-k}, X_t) = Cov(X_t, X_{t-k}) = \gamma_k$$
> Dělením $\gamma_0$ získáváme $\rho_{-k} = \rho_k$. $\blacksquare$
> 
> **Důkaz omezenosti:**
> Aplikujeme Cauchy-Schwarzovu nerovnost pro náhodné veličiny s konečným rozptylem, $|Cov(Y, Z)|^2 \le Var(Y)Var(Z)$. Dosazením $Y = X_t$ a $Z = X_{t-k}$:
> $$|\gamma_k|^2 = |Cov(X_t, X_{t-k})|^2 \le Var(X_t) Var(X_{t-k}) = \gamma_0 \cdot \gamma_0 = \gamma_0^2$$
> Odmocněním obou stran nerovnosti (přičemž $\gamma_0 \ge 0$) dostáváme:
> $$|\gamma_k| \le \gamma_0$$
> Vydělením kladným číslem $\gamma_0$ dostáváme $|\rho_k| \le 1$. $\blacksquare$

---

## 1.4 Základní stavební kámen: Bílý šum (White Noise)

> [!definition] Bílý šum (White Noise)
> Proces $\{\varepsilon_t\}_{t \in \mathbb{Z}}$ nazýváme **bílý šum** s nulovou střední hodnotou a rozptylem $\sigma^2$ (značíme $\varepsilon_t \sim WN(0, \sigma^2)$), jestliže:
> 1. $E[\varepsilon_t] = 0 \quad \forall t \in \mathbb{Z}$,
> 2. $Var(\varepsilon_t) = E[\varepsilon_t^2] = \sigma^2 \in (0, \infty) \quad \forall t \in \mathbb{Z}$,
> 3. $\gamma_k = Cov(\varepsilon_t, \varepsilon_{t-k}) = 0 \quad \forall k \neq 0$.

Autokovarianční funkce bílého šumu má tvar:
$$\gamma_k = \begin{cases} \sigma^2 & \text{pro } k = 0 \\ 0 & \text{pro } k \neq 0 \end{cases}$$
ACF bílého šumu je:
$$\rho_k = \begin{cases} 1 & \text{pro } k = 0 \\ 0 & \text{pro } k \neq 0 \end{cases}$$

### Hierarchie nezávislosti bílého šumu
1. **Slabý bílý šum ($WN(0, \sigma^2)$):** Nekorelované veličiny v čase ($Cov(\varepsilon_t, \varepsilon_s) = 0$ pro $t \neq s$). Mohou však být nelineárně závislé (např. $Cov(\varepsilon_t^2, \varepsilon_s^2) \neq 0$, typické pro modely ARCH/GARCH).
2. **Nezávislý bílý šum (i.i.d. šum):** $\varepsilon_t \sim i.i.d. (0, \sigma^2)$. Veličiny jsou vzájemně zcela stochasticky nezávislé.
3. **Gaussovský bílý šum:** $\varepsilon_t \sim i.i.d. \, N(0, \sigma^2)$. Protože pro sdruženě normální veličiny platí ekvivalence mezi nekorelovaností a nezávislostí, je každý Gaussovský slabý bílý šum zároveň i.i.d. bílým šumem.

---

## 1.5 Operátorový kalkulus: Operátor zpoždění $L$ a diferenční operátor $\Delta$

> [!definition] Lag Operator (Operátor zpoždění)
> **Operátor zpoždění** (Lag operator, často značen též $B$ jako Backshift) je lineární operátor $L: \mathbb{R}^{\mathbb{Z}} \to \mathbb{R}^{\mathbb{Z}}$ definovaný vztahem:
> $$L X_t = X_{t-1}$$
> Opakovanou aplikací operátoru definujeme celočíselné mocniny:
> $$L^k X_t = X_{t-k}, \quad k \in \mathbb{N}$$
> Formálně klademe $L^0 = I$ (operátor identity, $L^0 X_t = X_t$). Pro $k < 0$ definujeme operátor předsunutí (lead operator): $L^{-k} X_t = X_{t+k}$.

### Algebraické vlastnosti operátoru $L$
1. **Linearita:** Pro libovolné konstanty $a, b \in \mathbb{R}$ platí $L(a X_t + b Y_t) = a L X_t + b L Y_t = a X_{t-1} + b Y_{t-1}$.
2. **Působení na konstantu:** Je-li $c \in \mathbb{R}$ konstanta, pak $L c = c$.
3. **Polynomiální zápis:** Mějme polynom $\Phi(z) = 1 - \phi_1 z - \phi_2 z^2 - \dots - \phi_p z^p$. Pak operátorový polynom $\Phi(L)$ aplikovaný na proces generuje:
   $$\Phi(L) X_t = (1 - \phi_1 L - \phi_2 L^2 - \dots - \phi_p L^p) X_t = X_t - \sum_{i=1}^p \phi_i X_{t-i}$$

> [!definition] Diferenční operátor $\Delta$
> Diferenční operátor prvního řádu je definován vztahem:
> $$\Delta X_t = X_t - X_{t-1} = (1 - L) X_t$$
> Diferenční operátor řádu $d \in \mathbb{N}$ je definován rekurzivně $\Delta^d = \Delta(\Delta^{d-1})$, což odpovídá:
> $$\Delta^d X_t = (1 - L)^d X_t = \sum_{k=0}^d \binom{d}{k} (-1)^k L^k X_t = \sum_{k=0}^d \binom{d}{k} (-1)^k X_{t-k}$$

---

# Přednáška 2: Autoregresní modely AR($p$) a PACF

## 2.1 Model AR(1): Rigorózní analýza

Model autoregrese prvního řádu AR(1) je definován stochastickou diferenční rovnicí:
$$X_t = c + \phi X_{t-1} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2)$$
kde $c \in \mathbb{R}$ je konstanta a $\phi \in \mathbb{R}$ je autoregresní parametr. Zapsáno pomocí operátoru zpoždění:
$$(1 - \phi L) X_t = c + \varepsilon_t$$

### 2.1.1 Kauzalita, stabilita a charakteristický polynom

> [!definition] Kauzalita procesu
> Proces $\{X_t\}$ definovaný rovnicí $\Phi(L)X_t = \varepsilon_t$ nazýváme **kauzální** (vzhledem k $\{\varepsilon_t\}$), jestliže lze $X_t$ vyjádřit jako lineární kombinaci současných a minulých šoků:
> $$X_t = \sum_{j=0}^\infty \psi_j \varepsilon_{t-j}, \quad \text{kde } \sum_{j=0}^\infty |\psi_j| < \infty$$
> (neboli $X_t$ je funkcí výhradně minulosti a přítomnosti, nezávisí na budoucích šocích $\varepsilon_{t+1}, \varepsilon_{t+2}, \dots$).

Uvažujme charakteristický polynom přidružený k AR(1):
$$\Phi(z) = 1 - \phi z, \quad z \in \mathbb{C}$$
Hledáme kořen charakteristické rovnice $\Phi(z) = 0$:
$$1 - \phi z = 0 \implies z_0 = \frac{1}{\phi} \quad (\text{pro } \phi \neq 0)$$

> [!theorem] Podmínka kauzality pro AR(1)
> Proces AR(1) je kauzální a slabě stacionární právě tehdy, když leží kořen charakteristického polynomu **vně jednotkového kruhu** v komplexní rovině:
> $$|z_0| > 1 \iff \left|\frac{1}{\phi}\right| > 1 \iff |\phi| < 1$$

> [!proof] Odvození nekonečné MA reprezentace zpětnou substitucí
> Předpokládejme $|\phi| < 1$. Vyjádřeme $X_t$ rekurzivně:
> $$X_t = c + \phi X_{t-1} + \varepsilon_t$$
> Dosadíme za $X_{t-1} = c + \phi X_{t-2} + \varepsilon_{t-1}$:
> $$X_t = c + \phi (c + \phi X_{t-2} + \varepsilon_{t-1}) + \varepsilon_t = c(1 + \phi) + \varepsilon_t + \phi \varepsilon_{t-1} + \phi^2 X_{t-2}$$
> Dalším dosazením za $X_{t-2} = c + \phi X_{t-3} + \varepsilon_{t-2}$:
> $$X_t = c(1 + \phi + \phi^2) + \varepsilon_t + \phi \varepsilon_{t-1} + \phi^2 \varepsilon_{t-2} + \phi^3 X_{t-3}$$
> Po $k$ krocích matematickou indukcí:
> $$X_t = c \sum_{j=0}^{k-1} \phi^j + \sum_{j=0}^{k-1} \phi^j \varepsilon_{t-j} + \phi^k X_{t-k}$$
> Provedeme limitní přechod $k \to \infty$. 
> 1. Pro první člen platí součet geometrické řady:
>    $$\lim_{k \to \infty} c \sum_{j=0}^{k-1} \phi^j = c \frac{1}{1 - \phi}$$
> 2. Pro reziduální člen $\phi^k X_{t-k}$ vyšetříme konvergenci v kvadratickém středu ($L^2$):
>    $$E[(\phi^k X_{t-k})^2] = \phi^{2k} E[X_{t-k}^2]$$
>    Za předpokladu stacionarity je $E[X_{t-k}^2] = \text{konst.} < \infty$. Protože $|\phi| < 1$, platí $\lim_{k \to \infty} \phi^{2k} = 0$. Tedy $\phi^k X_{t-k} \xrightarrow{L^2} 0$.
> 3. Nekonečná řada $\sum_{j=0}^\infty \phi^j \varepsilon_{t-j}$ konverguje v $L^2$ i skoro jistě, neboť koeficienty splňují $\sum_{j=0}^\infty |\phi^j| = \frac{1}{1 - |\phi|} < \infty$.
>
> Dostáváme explicitní kauzální reprezentaci procesu:
> $$X_t = \frac{c}{1 - \phi} + \sum_{j=0}^\infty \phi^j \varepsilon_{t-j} \quad \blacksquare$$

---

### 2.1.2 Odvození momentů procesu AR(1)

Nechť $|\phi| < 1$.

#### Střední hodnota:
Aplikujeme operátor střední hodnoty na obě strany původní rovnice:
$$E[X_t] = E[c + \phi X_{t-1} + \varepsilon_t] = c + \phi E[X_{t-1}] + E[\varepsilon_t]$$
Ze stacionarity plyne $E[X_t] = E[X_{t-1}] = \mu$. Jelikož $E[\varepsilon_t] = 0$:
$$\mu = c + \phi \mu \implies \mu(1 - \phi) = c \implies \mu = \frac{c}{1 - \phi}$$

Centrujme proces definováním $\tilde{X}_t = X_t - \mu$. 
Rovnice procesu v centrovaném tvaru:
$$\tilde{X}_t = \phi \tilde{X}_{t-1} + \varepsilon_t$$

#### Rozptyl ($\gamma_0$):
Umocníme obě strany centrované rovnice na druhou a aplikujeme střední hodnotu:
$$\tilde{X}_t^2 = (\phi \tilde{X}_{t-1} + \varepsilon_t)^2 = \phi^2 \tilde{X}_{t-1}^2 + 2\phi \tilde{X}_{t-1}\varepsilon_t + \varepsilon_t^2$$
$$E[\tilde{X}_t^2] = \phi^2 E[\tilde{X}_{t-1}^2] + 2\phi E[\tilde{X}_{t-1}\varepsilon_t] + E[\varepsilon_t^2]$$
Využijeme následující vlastnosti:
1. $E[\tilde{X}_t^2] = E[\tilde{X}_{t-1}^2] = \gamma_0$ (stacionarita).
2. $E[\tilde{X}_{t-1}\varepsilon_t] = 0$, protože z kauzality je $\tilde{X}_{t-1} = \sum_{j=0}^\infty \phi^j \varepsilon_{t-1-j}$, což závisí pouze na šocích do času $t-1$. Tyto šoky jsou s budoucím šokem $\varepsilon_t$ nekorelované.
3. $E[\varepsilon_t^2] = \sigma^2$.

Dosazením:
$$\gamma_0 = \phi^2 \gamma_0 + 0 + \sigma^2 \implies \gamma_0(1 - \phi^2) = \sigma^2$$
$$\gamma_0 = \frac{\sigma^2}{1 - \phi^2}$$
Jelikož $\gamma_0 = Var(X_t) > 0$ a $\sigma^2 > 0$, musí nutně platit $1 - \phi^2 > 0 \implies |\phi| < 1$.

#### Autokovariance ($\gamma_k$) a Autokorelace ($\rho_k$):
Vynásobíme centrovanou rovnici $\tilde{X}_t = \phi \tilde{X}_{t-1} + \varepsilon_t$ veličinou $\tilde{X}_{t-k}$ (pro $k \ge 1$) a aplikujeme střední hodnotu:
$$E[\tilde{X}_t \tilde{X}_{t-k}] = \phi E[\tilde{X}_{t-1} \tilde{X}_{t-k}] + E[\varepsilon_t \tilde{X}_{t-k}]$$
Dle definice ACVF:
* $E[\tilde{X}_t \tilde{X}_{t-k}] = \gamma_k$,
* $E[\tilde{X}_{t-1} \tilde{X}_{t-k}] = Cov(X_{t-1}, X_{(t-1)-(k-1)}) = \gamma_{k-1}$,
* $E[\varepsilon_t \tilde{X}_{t-k}] = 0$ pro všechna $k \ge 1$ (opět z kauzality).

Získáváme homogenní lineární diferenční rovnici prvního řádu:
$$\gamma_k = \phi \gamma_{k-1}, \quad \forall k \ge 1$$
Rekurzivním rozvojem z počáteční podmínky $\gamma_0$:
$$\gamma_k = \phi^k \gamma_0 = \phi^k \frac{\sigma^2}{1 - \phi^2}, \quad k \ge 0$$
Pro obecné $k \in \mathbb{Z}$ využijeme sudost ($\gamma_k = \gamma_{-k}$):
$$\gamma_k = \frac{\sigma^2}{1 - \phi^2} \phi^{|k|}$$
Autokorelační funkci získáme vydělením $\gamma_0$:
$$\rho_k = \frac{\gamma_k}{\gamma_0} = \phi^{|k|}$$

> [!observation] Chování ACF u AR(1)
> 1. Pro $0 < \phi < 1$: $\rho_k$ klesá geometricky k nule, všechny hodnoty jsou kladné.
> 2. Pro $-1 < \phi < 0$: $\rho_k$ alternuje znaménka ($\rho_1 < 0, \rho_2 > 0, \dots$) a geometricky v absolutní hodnotě konverguje k nule.

---

## 2.2 Obecný model AR($p$) a Yule-Walkerovy rovnice

Obecný autoregresní model řádu $p$, AR($p$), je definován jako:
$$X_t = c + \phi_1 X_{t-1} + \phi_2 X_{t-2} + \dots + \phi_p X_{t-p} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2)$$
Operátorově:
$$\Phi(L) X_t = c + \varepsilon_t, \quad \Phi(L) = 1 - \sum_{j=1}^p \phi_j L^j$$

### Podmínka kauzality pro AR($p$)
Nechť $\Phi(z) = 1 - \phi_1 z - \phi_2 z^2 - \dots - \phi_p z^p$ je charakteristický polynom.
Proces AR($p$) je kauzální a slabě stacionární právě tehdy, když **všechny kořeny** rovnice $\Phi(z) = 0$ leží vně jednotkového kruhu v komplexní rovině:
$$\Phi(z) = 0 \implies |z| > 1$$

*(Pozor na alternativní zápis: Pokud je polynom definován jako $\lambda^p - \phi_1 \lambda^{p-1} - \dots - \phi_p = 0$, kde $\lambda = \frac{1}{z}$, pak musí všechny vlastní hodnoty $\lambda$ ležet uvnitř jednotkového kruhu, tedy $|\lambda| < 1$.)*

### Odvození Yule-Walkerových rovnic
Předpokládejme centrovaný proces $\tilde{X}_t = \sum_{j=1}^p \phi_j \tilde{X}_{t-j} + \varepsilon_t$.
Vynásobíme rovnici členem $\tilde{X}_{t-k}$ pro $k \ge 0$ a vezmeme střední hodnotu:
$$E[\tilde{X}_t \tilde{X}_{t-k}] = \sum_{j=1}^p \phi_j E[\tilde{X}_{t-j} \tilde{X}_{t-k}] + E[\varepsilon_t \tilde{X}_{t-k}]$$
Z definice $\gamma$ a faktu, že $E[\varepsilon_t \tilde{X}_{t-k}] = 0$ pro $k > 0$ a $E[\varepsilon_t \tilde{X}_t] = \sigma^2$ pro $k = 0$:

1. **Pro $k = 0$:**
   $$\gamma_0 = \sum_{j=1}^p \phi_j \gamma_{-j} + \sigma^2 = \sum_{j=1}^p \phi_j \gamma_j + \sigma^2$$
2. **Pro $k \ge 1$:**
   $$\gamma_k = \sum_{j=1}^p \phi_j \gamma_{k-j}$$

Vydělíme-li rovnice pro $k \ge 1$ rozptylem $\gamma_0$, obdržíme **Yule-Walkerův systém pro autokorelace**:
$$\rho_k = \phi_1 \rho_{k-1} + \phi_2 \rho_{k-2} + \dots + \phi_p \rho_{k-p}, \quad \forall k \ge 1$$
Pro $k = 1, 2, \dots, p$ lze systém zapsat maticově:
$$\begin{pmatrix}
1 & \rho_1 & \rho_2 & \dots & \rho_{p-1} \\
\rho_1 & 1 & \rho_1 & \dots & \rho_{p-2} \\
\rho_2 & \rho_1 & 1 & \dots & \rho_{p-3} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
\rho_{p-1} & \rho_{p-2} & \rho_{p-3} & \dots & 1
\end{pmatrix}
\begin{pmatrix}
\phi_1 \\ \phi_2 \\ \phi_3 \\ \vdots \\ \phi_p
\end{pmatrix}
=
\begin{pmatrix}
\rho_1 \\ \rho_2 \\ \rho_3 \\ \vdots \\ \rho_p
\end{pmatrix}$$
Tento systém má Toeplitzovu strukturu. Pokud je proces stacionární, je matice korelací pozitivně definitní a regulární, což zaručuje existenci jednoznačného řešení pro vektor parametrů $\boldsymbol{\phi} = (\phi_1, \dots, \phi_p)'$.

---

## 2.3 Parciální autokorelační funkce (PACF)

ACF procesů AR($p$) vykazuje nekonečnou paměť – hodnoty $\rho_k$ exponenciálně nebo tlumenými oscilacemi doznívají, ale nikdy se přesně nevynulují. Abychom identifikovali skutečný řád autoregrese $p$, potřebujeme nástroj, který odfiltruje nepřímou závislost přenášenou přes mezilehlé členy.

> [!definition] Parciální autokorelační funkce (PACF)
> Nechť $\{X_t\}$ je slabě stacionární proces s nulovou střední hodnotou. **Parciální autokorelace** řádu $k$, značená $\alpha_k$ (nebo $\phi_{kk}$), je korelace mezi $X_t$ a $X_{t-k}$ očištěná o lineární vliv mezilehlých náhodných veličin $\{X_{t-1}, X_{t-2}, \dots, X_{t-k+1}\}$.
> 
> Formálně pomocí nejlepší lineární projekce $P_{\mathcal{H}}$ do Hilbertova prostoru generovaného těmito mezilehlými proměnnými:
> $$\alpha_1 = Corr(X_t, X_{t-1}) = \rho_1$$
> $$\alpha_k = Corr\Big( X_t - P_{\{X_{t-1}, \dots, X_{t-k+1}\}} X_t, \; X_{t-k} - P_{\{X_{t-1}, \dots, X_{t-k+1}\}} X_{t-k} \Big), \quad k \ge 2$$

V praxi se $\alpha_k$ získá jako poslední koeficient $\phi_{kk}$ v lineární regresi $X_t$ na jejích $k$ zpoždění:
$$X_t = \phi_{k1} X_{t-1} + \phi_{k2} X_{t-2} + \dots + \phi_{kk} X_{t-k} + u_t$$

Dle **Cramerova pravidla** aplikovaného na Yule-Walkerův systém řádu $k$:
$$\alpha_k \equiv \phi_{kk} = \frac{\det(P_k^*)}{\det(P_k)}$$
kde $P_k$ je autokorelační matice řádu $k \times k$:
$$P_k = \begin{pmatrix}
1 & \rho_1 & \dots & \rho_{k-1} \\
\rho_1 & 1 & \dots & \rho_{k-2} \\
\vdots & \vdots & \ddots & \vdots \\
\rho_{k-1} & \rho_{k-2} & \dots & 1
\end{pmatrix}$$
a $P_k^*$ je matice vzniklá nahrazením posledního sloupce matice $P_k$ vektorem $(\rho_1, \rho_2, \dots, \rho_k)'$.

> [!theorem] Vlastnost useknutí PACF pro AR($p$)
> Pro stacionární autoregresní proces AR($p$) platí:
> 1. $\alpha_p = \phi_p \neq 0$,
> 2. $\alpha_k = 0 \quad \forall k > p$.
> 
> PACF se po zpoždění $p$ **skokově usekne na nulu**.

---

## 2.4 Kompletně řešený příklad: AR(2) proces

Uvažujme proces AR(2) bez konstanty:
$$X_t = 0.7 X_{t-1} - 0.1 X_{t-2} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, 1)$$

### Krok 1: Test kauzality / stacionarity
Parametry jsou $\phi_1 = 0.7$, $\phi_2 = -0.1$.
Charakteristický polynom:
$$\Phi(z) = 1 - 0.7 z - (-0.1) z^2 = 0.1 z^2 - 0.7 z + 1$$
Hledáme kořeny $\Phi(z) = 0$:
$$0.1 z^2 - 0.7 z + 1 = 0 \iff z^2 - 7z + 10 = 0$$
Rozklad kvadratického trojčlenu:
$$(z - 2)(z - 5) = 0 \implies z_1 = 2, \quad z_2 = 5$$
Ověření velikosti kořenů:
$$|z_1| = 2 > 1, \quad |z_2| = 5 > 1$$
Oba kořeny leží striktně vně jednotkového kruhu. Proces je **kauzální a slabě stacionární**.

### Krok 2: Odvození autokorelací $\rho_1$ a $\rho_2$ přes Yule-Walker
Yule-Walkerovy rovnice pro $k = 1$ a $k = 2$:
$$\rho_1 = \phi_1 \rho_0 + \phi_2 \rho_1 = \phi_1 + \phi_2 \rho_1 \quad (\text{protože } \rho_0 = 1, \rho_{-1} = \rho_1)$$
$$\rho_2 = \phi_1 \rho_1 + \phi_2 \rho_0 = \phi_1 \rho_1 + \phi_2$$

Dosadíme numerické hodnoty:
$$\rho_1 = 0.7 - 0.1 \rho_1 \implies 1.1 \rho_1 = 0.7 \implies \rho_1 = \frac{0.7}{1.1} = \frac{7}{11} \approx 0.6364$$
Nyní dosadíme vypočtené $\rho_1$ do rovnice pro $\rho_2$:
$$\rho_2 = 0.7 \left(\frac{7}{11}\right) - 0.1 = \frac{4.9}{11} - \frac{1.1}{11} = \frac{3.8}{11} = \frac{38}{110} = \frac{19}{55} \approx 0.3455$$

### Krok 3: Výpočet nepodmíněného rozptylu $\gamma_0$
Yule-Walkerova rovnice pro $k = 0$:
$$\gamma_0 = \phi_1 \gamma_1 + \phi_2 \gamma_2 + \sigma^2$$
Využijeme vztahu $\gamma_k = \rho_k \gamma_0$:
$$\gamma_0 = \phi_1 \rho_1 \gamma_0 + \phi_2 \rho_2 \gamma_0 + \sigma^2$$
$$\gamma_0 (1 - \phi_1 \rho_1 - \phi_2 \rho_2) = \sigma^2 \implies \gamma_0 = \frac{\sigma^2}{1 - \phi_1 \rho_1 - \phi_2 \rho_2}$$
Dosadíme zlomky:
$$1 - 0.7\left(\frac{7}{11}\right) - (-0.1)\left(\frac{19}{55}\right) = 1 - \frac{49}{110} + \frac{19}{550} = 1 - \frac{245}{550} + \frac{19}{550} = \frac{550 - 226}{550} = \frac{324}{550}$$
Jelikož $\sigma^2 = 1$:
$$\gamma_0 = \frac{1}{\frac{324}{550}} = \frac{550}{324} = \frac{275}{162} \approx 1.6975$$

### Krok 4: Výpočet PACF koeficientů $\alpha_1, \alpha_2, \alpha_3$
* $\alpha_1 = \rho_1 = \frac{7}{11} \approx 0.6364$.
* Pro $\alpha_2$ použijeme determinantový vzorec (Cramerovo pravidlo pro $k=2$):
  $$\alpha_2 = \frac{\det \begin{pmatrix} 1 & \rho_1 \\ \rho_1 & \rho_2 \end{pmatrix}}{\det \begin{pmatrix} 1 & \rho_1 \\ \rho_1 & 1 \end{pmatrix}} = \frac{\rho_2 - \rho_1^2}{1 - \rho_1^2}$$
  Dosadíme:
  $$\rho_1^2 = \left(\frac{7}{11}\right)^2 = \frac{49}{121}$$
  $$\rho_2 - \rho_1^2 = \frac{19}{55} - \frac{49}{121} = \frac{19 \cdot 11 - 49 \cdot 5}{605} = \frac{209 - 245}{605} = -\frac{36}{605}$$
  $$1 - \rho_1^2 = 1 - \frac{49}{121} = \frac{72}{121} = \frac{360}{605}$$
  $$\alpha_2 = \frac{-\frac{36}{605}}{\frac{360}{605}} = -\frac{36}{360} = -0.1$$
  *Pozorování:* $\alpha_2 = \phi_2 = -0.1$, což přesně potvrzuje teorii.
* Pro $k = 3$: Z vlastnosti useknutí PACF u AR(2) přímo plyne:
  $$\alpha_3 = 0, \quad \alpha_4 = 0, \quad \dots$$

---

# Přednáška 3: Modely klouzavých průměrů MA($q$) a Invertibilita

## 3.1 Model MA(1): Rigorózní analýza

Model klouzavých průměrů prvního řádu MA(1) je definován jako:
$$X_t = \mu + \varepsilon_t + \theta \varepsilon_{t-1}, \quad \varepsilon_t \sim WN(0, \sigma^2)$$
kde $\mu \in \mathbb{R}$ je střední hodnota a $\theta \in \mathbb{R}$ je parametr klouzavých průměrů.
Operátorový zápis:
$$X_t = \mu + (1 + \theta L) \varepsilon_t = \mu + \Theta(L) \varepsilon_t, \quad \text{kde } \Theta(L) = 1 + \theta L$$

### 3.1.1 Stacionarita a momenty MA(1)

> [!theorem] Stacionarita MA(1)
> Proces MA(1) je **vždy slabě stacionární** pro jakoukoliv konečnou hodnotu parametru $\theta \in \mathbb{R}$.

> [!proof] Odvození momentů MA(1) krok za krokem
> **1. Střední hodnota:**
> $$E[X_t] = E[\mu + \varepsilon_t + \theta \varepsilon_{t-1}] = \mu + E[\varepsilon_t] + \theta E[\varepsilon_{t-1}] = \mu + 0 + 0 = \mu$$
> 
> **2. Rozptyl ($\gamma_0$):**
> Definujme $\tilde{X}_t = X_t - \mu = \varepsilon_t + \theta \varepsilon_{t-1}$.
> $$\gamma_0 = E[\tilde{X}_t^2] = E[(\varepsilon_t + \theta \varepsilon_{t-1})^2] = E[\varepsilon_t^2 + 2\theta \varepsilon_t \varepsilon_{t-1} + \theta^2 \varepsilon_{t-1}^2]$$
> Z linearity střední hodnoty a vlastností bílého šumu ($E[\varepsilon_t^2] = E[\varepsilon_{t-1}^2] = \sigma^2$ a $E[\varepsilon_t \varepsilon_{t-1}] = 0$):
> $$\gamma_0 = \sigma^2 + 2\theta(0) + \theta^2 \sigma^2 = \sigma^2(1 + \theta^2)$$
> 
> **3. Autokovariance prvního řádu ($\gamma_1$):**
> $$\gamma_1 = E[\tilde{X}_t \tilde{X}_{t-1}] = E[(\varepsilon_t + \theta \varepsilon_{t-1})(\varepsilon_{t-1} + \theta \varepsilon_{t-2})]$$
> Roznásobením:
> $$\gamma_1 = E[\varepsilon_t \varepsilon_{t-1} + \theta \varepsilon_t \varepsilon_{t-2} + \theta \varepsilon_{t-1}^2 + \theta^2 \varepsilon_{t-1}\varepsilon_{t-2}]$$
> Aplikací střední hodnoty (všechny smíšené členy mají nulovou střední hodnotu):
> $$\gamma_1 = 0 + 0 + \theta E[\varepsilon_{t-1}^2] + 0 = \theta \sigma^2$$
> 
> **4. Autokovariance řádu $k \ge 2$ ($\gamma_k$):**
> $$\gamma_k = E[\tilde{X}_t \tilde{X}_{t-k}] = E[(\varepsilon_t + \theta \varepsilon_{t-1})(\varepsilon_{t-k} + \theta \varepsilon_{t-k-1})]$$
> Pro $k \ge 2$ jsou indexy $\{t, t-1\}$ a $\{t-k, t-k-1\}$ disjunktní množiny. Jelikož šoky jsou v různých časech nekorelované:
> $$\gamma_k = 0, \quad \forall k \ge 2$$
> 
> Všechny momenty ($E[X_t]$, $\gamma_0$, $\gamma_k$) jsou nezávislé na čase $t$, proces je tedy slabě stacionární. $\blacksquare$

### Autokorelační funkce MA(1)
$$\rho_k = \frac{\gamma_k}{\gamma_0} = \begin{cases} 
1 & \text{pro } k = 0 \\
\frac{\theta \sigma^2}{\sigma^2(1 + \theta^2)} = \frac{\theta}{1 + \theta^2} & \text{pro } |k| = 1 \\
0 & \text{pro } |k| \ge 2 
\end{cases}$$

> [!observation] Vlastnost useknutí ACF pro MA(1)
> ACF procesu MA(1) má **ostrý zlom (cut-off)**: po zpoždění $k = 1$ je přesně rovna nule.

---

## 3.2 Invertibilita: Důvod, definice a důkaz

Zkoumejme funkci $\rho_1(\theta) = \frac{\theta}{1 + \theta^2}$.
Dosadíme-li parametr $\theta$ a následně jeho převrácenou hodnotu $\frac{1}{\theta}$:
$$\rho_1\left(\frac{1}{\theta}\right) = \frac{\frac{1}{\theta}}{1 + \left(\frac{1}{\theta}\right)^2} = \frac{\frac{1}{\theta}}{\frac{\theta^2 + 1}{\theta^2}} = \frac{\theta^2}{\theta(\theta^2 + 1)} = \frac{\theta}{1 + \theta^2} = \rho_1(\theta)$$

> [!warning] Problém identifikovatelnosti
> Dva procesy s parametry $\theta$ a $\frac{1}{\theta}$ mají naprosto **identickou autokorelační funkci**. 
> *Příklad:* Model s $\theta = 0.5$ a model s $\theta = 2.0$ mají oba $\rho_1 = \frac{0.5}{1 + 0.25} = 0.4$. 
> Pouze na základě pozorovaných dat $\{X_t\}$ a jejich korelací nelze bez dodatečného kritéria rozhodnout, který model generoval data. Tímto kritériem je **invertibilita**.

> [!definition] Invertibilita procesu
> Proces $\{X_t\}$ generovaný vztahem $X_t = \mu + \Theta(L)\varepsilon_t$ nazýváme **invertibilní**, jestliže jej lze vyjádřit jako kauzální autoregresní proces nekonečného řádu AR($\infty$):
> $$\varepsilon_t = \sum_{j=0}^\infty \pi_j (X_{t-j} - \mu), \quad \text{kde } \sum_{j=0}^\infty |\pi_j| < \infty, \; \pi_0 = 1$$
> Neformálně: Současný šok $\varepsilon_t$ lze jednoznačně zrekonstruovat z minulé a současné historie pozorovaného procesu $\{X_s\}_{s \le t}$.

> [!theorem] Podmínka invertibility pro MA(1)
> Proces MA(1) je invertibilní právě tehdy, když kořen charakteristického polynomu $\Theta(z) = 1 + \theta z = 0$ leží vně jednotkového kruhu v komplexní rovině:
> $$|z_0| > 1 \iff \left|-\frac{1}{\theta}\right| > 1 \iff |\theta| < 1$$

> [!proof] Konstruktivní důkaz invertibility MA(1)
> Uvažujme $\tilde{X}_t = (1 + \theta L) \varepsilon_t$. Formálně vyjádříme $\varepsilon_t$:
> $$\varepsilon_t = (1 + \theta L)^{-1} \tilde{X}_t$$
> Zkoumejme operátor $(1 + \theta L)^{-1}$. Využijeme rozvoj do formální geometrické řady:
> $$\frac{1}{1 - z} = \sum_{j=0}^\infty z^j \quad \text{pro } |z| < 1$$
> Položme $z = -\theta L$. Pak za předpokladu $|\theta| < 1$:
> $$(1 + \theta L)^{-1} = \sum_{j=0}^\infty (-\theta L)^j = \sum_{j=0}^\infty (-\theta)^j L^j = 1 - \theta L + \theta^2 L^2 - \theta^3 L^3 + \dots$$
> Aplikujeme operátor na $\tilde{X}_t$:
> $$\varepsilon_t = \sum_{j=0}^\infty (-\theta)^j \tilde{X}_{t-j} = \tilde{X}_t - \theta \tilde{X}_{t-1} + \theta^2 \tilde{X}_{t-2} - \theta^3 \tilde{X}_{t-3} + \dots$$
> Jelikož $|\theta| < 1$, koeficienty $\pi_j = (-\theta)^j$ tvoří absolutně konvergentní geometrickou řadu:
> $$\sum_{j=0}^\infty |\pi_j| = \sum_{j=0}^\infty |\theta|^j = \frac{1}{1 - |\theta|} < \infty$$
> Tím je proces reprezentován jako konvergentní $AR(\infty)$ a je invertibilní. $\blacksquare$

### Důsledek pro neinvertibilní proces ($|\theta| > 1$)
Kdybychom provedli formální inverzi pro $|\theta| > 1$, operátor by musel být rozvinut pomocí budoucího operátoru $L^{-1}$ (operátoru předsunutí):
$$\varepsilon_t = \sum_{j=1}^\infty \left(-\frac{1}{\theta}\right)^j \tilde{X}_{t+j}$$
Šok $\varepsilon_t$ v čase $t$ by závisel na budoucích hodnotách $X_{t+1}, X_{t+2}, \dots$, což znemožňuje praktickou predikci a konstrukci věrohodnostní funkce v čase.

---

## 3.3 Obecný model MA($q$)

Obecný model klouzavých průměrů řádu $q$, MA($q$):
$$X_t = \mu + \varepsilon_t + \theta_1 \varepsilon_{t-1} + \theta_2 \varepsilon_{t-2} + \dots + \theta_q \varepsilon_{t-q} = \mu + \Theta(L)\varepsilon_t$$
kde $\Theta(L) = 1 + \theta_1 L + \theta_2 L^2 + \dots + \theta_q L^q$.

### Momenty MA($q$):
1. $E[X_t] = \mu$
2. Autokovariance:
   $$\gamma_k = \begin{cases}
   \sigma^2 \sum_{j=0}^{q-k} \theta_j \theta_{j+k} & \text{pro } k = 0, 1, \dots, q \quad (\text{přičemž } \theta_0 = 1) \\
   0 & \text{pro } k > q
   \end{cases}$$
3. Autokorelace:
   $$\rho_k = \begin{cases}
   \frac{\sum_{j=0}^{q-k} \theta_j \theta_{j+k}}{\sum_{j=0}^q \theta_j^2} & \text{pro } k \le q \\
   0 & \text{pro } k > q
   \end{cases}$$

### Podmínka invertibility MA($q$):
Všechny kořeny charakteristického polynomu $\Theta(z) = 1 + \theta_1 z + \dots + \theta_q z^q = 0$ musí ležet **vně jednotkového kruhu** ($|z_i| > 1$ pro všechna $i = 1, \dots, q$).

---

## 3.4 Kompletně řešený příklad: MA(2) proces

Mějme proces MA(2):
$$X_t = \varepsilon_t + 0.5 \varepsilon_{t-1} - 0.3 \varepsilon_{t-2}, \quad \varepsilon_t \sim WN(0, 4)$$
Parametry: $\mu = 0$, $\theta_1 = 0.5$, $\theta_2 = -0.3$, $\sigma^2 = 4$.

### Krok 1: Test invertibility
Charakteristický polynom:
$$\Theta(z) = 1 + 0.5 z - 0.3 z^2 = 0 \iff -0.3 z^2 + 0.5 z + 1 = 0 \iff 3z^2 - 5z - 10 = 0$$
Diskriminant kvadratické rovnice:
$$D = (-5)^2 - 4 \cdot 3 \cdot (-10) = 25 + 120 = 145$$
Kořeny:
$$z_{1,2} = \frac{-(-5) \pm \sqrt{145}}{2 \cdot 3} = \frac{5 \pm \sqrt{145}}{6}$$
Jelikož $\sqrt{145} \approx 12.0416$:
$$z_1 = \frac{5 + 12.0416}{6} = \frac{17.0416}{6} \approx 2.84 > 1$$
$$z_2 = \frac{5 - 12.0416}{6} = \frac{-7.0416}{6} \approx -1.173 \implies |z_2| = 1.173 > 1$$
Oba kořeny leží v absolutní hodnotě vně jednotkového kruhu ($|z_1| > 1, |z_2| > 1$). Proces je **invertibilní**.

### Krok 2: Explicitní výpočet autokovariancí $\gamma_0, \gamma_1, \gamma_2, \gamma_3$
Aplikujeme obecný vzorec $\gamma_k = \sigma^2 \sum_{j=0}^{2-k} \theta_j \theta_{j+k}$ s $\theta_0 = 1, \theta_1 = 0.5, \theta_2 = -0.3$:

* **Rozptyl ($k = 0$):**
  $$\gamma_0 = \sigma^2 (\theta_0^2 + \theta_1^2 + \theta_2^2) = 4 \cdot \big(1^2 + 0.5^2 + (-0.3)^2\big) = 4 \cdot (1 + 0.25 + 0.09) = 4 \cdot 1.34 = 5.36$$
* **Kovariance pro lag 1 ($k = 1$):**
  $$\gamma_1 = \sigma^2 (\theta_0 \theta_1 + \theta_1 \theta_2) = 4 \cdot \big(1 \cdot 0.5 + 0.5 \cdot (-0.3)\big) = 4 \cdot (0.5 - 0.15) = 4 \cdot 0.35 = 1.40$$
* **Kovariance pro lag 2 ($k = 2$):**
  $$\gamma_2 = \sigma^2 (\theta_0 \theta_2) = 4 \cdot \big(1 \cdot (-0.3)\big) = 4 \cdot (-0.3) = -1.20$$
* **Kovariance pro lag 3 a vyšší ($k \ge 3$):**
  $$\gamma_k = 0 \quad \forall k \ge 3$$

### Krok 3: Výpočet ACF
$$\rho_0 = 1$$
$$\rho_1 = \frac{\gamma_1}{\gamma_0} = \frac{1.40}{5.36} = \frac{140}{536} = \frac{35}{134} \approx 0.2612$$
$$\rho_2 = \frac{\gamma_2}{\gamma_0} = \frac{-1.20}{5.36} = -\frac{120}{536} = -\frac{15}{67} \approx -0.2239$$
$$\rho_k = 0 \quad \forall k \ge 3$$

---

# Přednáška 4: Smíšené procesy ARMA($p,q$) a Woldova věta

## 4.1 Definice a reprezentace ARMA($p,q$)

> [!definition] Model ARMA($p,q$)
> Proces $\{X_t\}_{t \in \mathbb{Z}}$ je **autoregresní proces s klouzavými průměry** řádu $(p,q)$, značený ARMA($p,q$), jestliže splňuje diferenční rovnici:
> $$X_t = c + \phi_1 X_{t-1} + \dots + \phi_p X_{t-p} + \varepsilon_t + \theta_1 \varepsilon_{t-1} + \dots + \theta_q \varepsilon_{t-q}$$
> kde $\varepsilon_t \sim WN(0, \sigma^2)$. V operátorovém zápisu:
> $$\Phi(L) X_t = c + \Theta(L) \varepsilon_t$$
> s polynomy zpoždění:
> $$\Phi(L) = 1 - \sum_{i=1}^p \phi_i L^i, \quad \Theta(L) = 1 + \sum_{j=1}^q \theta_j L^j$$

### Podmínky pro ARMA($p,q$):
1. **Kauzální stacionarita:** Všechny kořeny $\Phi(z) = 0$ leží vně jednotkového kruhu ($|z| > 1$).
2. **Invertibilita:** Všechny kořeny $\Theta(z) = 0$ leží vně jednotkového kruhu ($|z| > 1$).
3. **Minimální realizace (ireducibilita):** Polynomy $\Phi(z)$ a $\Theta(z)$ **nemají společné kořeny**.

> [!warning] Past zrušení kořenů (Parameter Redundancy)
> Uvažujme model: $(1 - 0.5 L) X_t = (1 - 0.5 L) \varepsilon_t$.
> Formálně se jeví jako ARMA(1,1). Pokud však obě strany formálně vynásobíme operátorem $(1 - 0.5 L)^{-1}$, dostáváme:
> $$X_t = \varepsilon_t$$
> Jde o čistý bílý šum. Pokus o odhad parametrů ARMA(1,1) by vedl ke krachu numerické optimalizace (singulární informační matice), neboť parametry $\phi_1$ a $\theta_1$ nejsou separátně identifikovatelné podél přímky $\phi_1 = -\theta_1$.

---

## 4.2 Woldova dekompozice

Woldova věta je základním teoretickým pilířem moderní lineární analýzy časových řad. Říká, že jakýkoliv slabě stacionární proces lze bezezbytku rozložit na deterministickou a stochastickou část.

> [!theorem] Woldova věta o rozkladu (1938)
> Nechť $\{X_t\}_{t \in \mathbb{Z}}$ je libovolný slabě stacionární proces s nulovou střední hodnotou a konečným rozptylem. Pak lze $X_t$ jednoznačně vyjádřit ve tvaru:
> $$X_t = U_t + V_t = \sum_{j=0}^\infty \psi_j \varepsilon_{t-j} + V_t$$
> kde platí:
> 1. $\varepsilon_t \sim WN(0, \sigma^2)$ je inovace procesu definovaná jako chyba nejlepší lineární předpovědi $X_t$ na základě celé nekonečné minulosti: $\varepsilon_t = X_t - P_{\{X_{t-1}, X_{t-2}, \dots\}} X_t$.
> 2. $\psi_0 = 1$ a koeficienty splňují čtvercovou sumabilitu: $\sum_{j=0}^\infty \psi_j^2 < \infty$.
> 3. Proces $\{U_t\}$ je **čistě nedeterministický** (lineárně regulární).
> 4. Proces $\{V_t\}$ je **deterministický** – lze jej beze chyby předpovědět z jeho vlastní minulosti ($V_t = P_{\{V_{t-1}, V_{t-2}, \dots\}} V_t$). Příkladem jsou konstanty, harmonické vlny s fixní fází či deterministické polynomiální trendy.
> 5. Procesy $\{U_t\}$ a $\{V_t\}$ jsou vzájemně nekorelované: $Cov(U_t, V_s) = 0$ pro všechna $t, s \in \mathbb{Z}$.

> [!important] Důsledek pro ARMA modelování
> Pokud je proces čistě nedeterministický ($V_t = 0$), Woldova věta garantuje, že proces lze zapsat jako $MA(\infty)$. Modely ARMA($p,q$) pak slouží jako **parsimonní (úsporné) racionální aproximace** obecného nekonečného operátoru $\Psi(L) = \sum_{j=0}^\infty \psi_j L^j$:
> $$\Psi(L) \approx \frac{\Theta(L)}{\Phi(L)}$$
> Místo nekonečně mnoha neznámých parametrů $\psi_j$ odhadujeme pouze $p + q$ parametrů $(\phi_1, \dots, \phi_p, \theta_1, \dots, \theta_q)$.

---

## 4.3 Výpočet ACVF pro ARMA(1,1) krok za krokem

Mějme centrovaný proces ARMA(1,1):
$$X_t - \phi X_{t-1} = \varepsilon_t + \theta \varepsilon_{t-1}, \quad |\phi| < 1, \quad |\theta| < 1, \quad \phi + \theta \neq 0$$
Rozvineme $X_t$ do kauzální reprezentace:
$$X_t = (1 - \phi L)^{-1}(1 + \theta L)\varepsilon_t = (1 + \phi L + \phi^2 L^2 + \dots)(1 + \theta L)\varepsilon_t$$
$$X_t = \varepsilon_t + (\phi + \theta)\varepsilon_{t-1} + \phi(\phi + \theta)\varepsilon_{t-2} + \phi^2(\phi + \theta)\varepsilon_{t-3} + \dots$$
Koeficienty Woldovy reprezentace $X_t = \sum_{j=0}^\infty \psi_j \varepsilon_{t-j}$ tedy jsou:
$$\psi_0 = 1, \quad \psi_1 = \phi + \theta, \quad \psi_j = \phi^{j-1}(\phi + \theta) \quad \text{pro } j \ge 1$$

Odtud plyne klíčová vlastnost pro výpočet kovariancí mezi $X$ a budoucími/současnými šoky $\varepsilon$:
$$E[X_t \varepsilon_t] = \psi_0 \sigma^2 = \sigma^2$$
$$E[X_t \varepsilon_{t-1}] = \psi_1 \sigma^2 = (\phi + \theta)\sigma^2$$
$$E[X_t \varepsilon_{t-k}] = \psi_k \sigma^2 \quad \text{pro } k \ge 0$$
$$E[X_{t-k} \varepsilon_t] = 0 \quad \text{pro } k \ge 1 \quad (\text{z kauzality})$$

Nyní vynásobíme původní rovnici $X_t = \phi X_{t-1} + \varepsilon_t + \theta \varepsilon_{t-1}$ postupně veličinami $X_t, X_{t-1}, X_{t-2}$:

### 1. Rozptyl ($\gamma_0$):
Vynásobíme $X_t$ a vezmeme střední hodnotu:
$$\gamma_0 = E[X_t^2] = \phi E[X_{t-1} X_t] + E[\varepsilon_t X_t] + \theta E[\varepsilon_{t-1} X_t]$$
Dosadíme známé vztahy:
$$E[X_{t-1} X_t] = \gamma_1$$
$$E[\varepsilon_t X_t] = \sigma^2$$
$$E[\varepsilon_{t-1} X_t] = (\phi + \theta)\sigma^2$$
Máme první rovnici:
$$\gamma_0 = \phi \gamma_1 + \sigma^2 + \theta(\phi + \theta)\sigma^2 = \phi \gamma_1 + \sigma^2\big(1 + \theta\phi + \theta^2\big) \quad \text{--- (Rovnice 1)}$$

### 2. Autokovariance pro lag 1 ($\gamma_1$):
Vynásobíme rovnici $X_t$ členem $X_{t-1}$:
$$\gamma_1 = E[X_t X_{t-1}] = \phi E[X_{t-1}^2] + E[\varepsilon_t X_{t-1}] + \theta E[\varepsilon_{t-1} X_{t-1}]$$
Dosadíme:
$$E[X_{t-1}^2] = \gamma_0$$
$$E[\varepsilon_t X_{t-1}] = 0$$
$$E[\varepsilon_{t-1} X_{t-1}] = \sigma^2$$
Máme druhou rovnici:
$$\gamma_1 = \phi \gamma_0 + \theta \sigma^2 \quad \text{--- (Rovnice 2)}$$

### 3. Vyřešení soustavy pro $\gamma_0$ a $\gamma_1$:
Dosadíme (Rovnici 2) do (Rovnice 1):
$$\gamma_0 = \phi (\phi \gamma_0 + \theta \sigma^2) + \sigma^2(1 + \theta\phi + \theta^2)$$
$$\gamma_0 = \phi^2 \gamma_0 + \phi\theta\sigma^2 + \sigma^2(1 + \theta\phi + \theta^2)$$
Převedeme členy s $\gamma_0$ na levou stranu:
$$\gamma_0(1 - \phi^2) = \sigma^2 \big(1 + 2\phi\theta + \theta^2\big)$$
Vydělíme $(1 - \phi^2)$:
$$\gamma_0 = \sigma^2 \frac{1 + 2\phi\theta + \theta^2}{1 - \phi^2}$$

Nyní dosadíme tento rozptyl zpět do (Rovnice 2) pro výpočet $\gamma_1$:
$$\gamma_1 = \phi \left( \sigma^2 \frac{1 + 2\phi\theta + \theta^2}{1 - \phi^2} \right) + \theta \sigma^2 = \sigma^2 \left( \frac{\phi + 2\phi^2\theta + \phi\theta^2 + \theta(1 - \phi^2)}{1 - \phi^2} \right)$$
Upravíme čitatele:
$$\phi + 2\phi^2\theta + \phi\theta^2 + \theta - \theta\phi^2 = \phi + \theta + \phi^2\theta + \phi\theta^2 = (\phi + \theta) + \phi\theta(\phi + \theta) = (1 + \phi\theta)(\phi + \theta)$$
Dostáváme:
$$\gamma_1 = \sigma^2 \frac{(1 + \phi\theta)(\phi + \theta)}{1 - \phi^2}$$

### 4. Autokovariance pro $k \ge 2$:
Pro $k \ge 2$ násobíme $X_t = \phi X_{t-1} + \varepsilon_t + \theta \varepsilon_{t-1}$ členem $X_{t-k}$:
$$E[X_t X_{t-k}] = \phi E[X_{t-1} X_{t-k}] + E[\varepsilon_t X_{t-k}] + \theta E[\varepsilon_{t-1} X_{t-k}]$$
Jelikož $k \ge 2$, jsou oba šoky $\varepsilon_t$ a $\varepsilon_{t-1}$ striktně z budoucnosti vůči $X_{t-k}$, takže jejich střední hodnoty součinů jsou 0:
$$\gamma_k = \phi \gamma_{k-1}, \quad \forall k \ge 2$$
Rekurzivně:
$$\gamma_k = \phi^{k-1} \gamma_1, \quad \forall k \ge 2$$

Autokorelační funkce:
$$\rho_1 = \frac{\gamma_1}{\gamma_0} = \frac{(1 + \phi\theta)(\phi + \theta)}{1 + 2\phi\theta + \theta^2}$$
$$\rho_k = \phi \rho_{k-1} = \phi^{k-1} \rho_1, \quad \forall k \ge 2$$

> [!summary] Diagnostický profil modelů (ACF vs. PACF)
> | Model | ACF | PACF |
> | :--- | :--- | :--- |
> | **AR($p$)** | Doznívá (tlumené exponenciály či kosinusoidy) | **Usekne se** po zpoždění $p$ ($\alpha_k = 0 \;\forall k > p$) |
> | **MA($q$)** | **Usekne se** po zpoždění $q$ ($\rho_k = 0 \;\forall k > q$) | Doznívá (tlumené exponenciály či kosinusoidy) |
> | **ARMA($p,q$)** | Doznívá od lagu $q$ | Doznívá od lagu $p$ |

---

# Přednáška 5: Nestacionarita, Jednotkový kořen a ARIMA($p,d,q$)

## 5.1 Deterministický vs. Stochastický trend

Většina ekonomických a finančních řad v původních úrovních vykazuje silný rostoucí či klesající trend. Způsob, jakým je tento trend generován, zásadně určuje matematické vlastnosti procesu.

```
       DETERMINISTICKÝ TREND (TSP)                 STOCHASTICKÝ TREND (DSP)
       yt = α + βt + εt                           yt = yt-1 + β + εt
       
   yt ^             * Trajektorie             yt ^                 * Trajektorie
      │           *                              │              *
      │   *     *                                │      *     *  *
      │ *   * *                                  │   *    * *
      │───────────────── Trend                   │ *───────────────── Drift
      └─────────────────────> t                  └─────────────────────> t
      Šoky jsou TRANSITIVNÍ                      Šoky jsou PERMANENTNÍ
      (návrat k deterministické přímce)          (změna úrovně navždy)
```

### A. Trendově stacionární proces (TSP – Trend-Stationary Process)
$$y_t = \alpha + \beta t + X_t$$
kde $\alpha + \beta t$ je deterministická lineární funkce času a $X_t$ je stacionární proces s nulovou střední hodnotou (např. ARMA).
* **Odstranění trendu:** Pouhá regrese na čas $t$ (metoda OLS) a extrakce reziduí: $\hat{X}_t = y_t - \hat{\alpha} - \hat{\beta} t$.
* **Chování šoků:** Šoky mají **přechodný (tranzitorní) charakter**. Po odeznění šoku se řada vrací ke své deterministické trendové přímce.
* **Rozptyl:** $Var(y_t) = Var(X_t) = \text{konst.}$ – rozptyl je v čase ohraničený.

### B. Diferenčně stacionární proces (DSP – Difference-Stationary Process)
$$y_t = \beta + y_{t-1} + \varepsilon_t \iff (1 - L)y_t = \beta + \varepsilon_t$$
Proces obsahuje **jednotkový kořen** ($1 - L = \Delta$).
* **Odstranění trendu:** Obyčejná regrese na čas selhává (vede ke spurious regression). Trend se musí odstranit **diferencováním**: $\Delta y_t = y_t - y_{t-1} = \beta + \varepsilon_t$.
* **Chování šoků:** Šoky mají **permanentní (trvalý) charakter**. Každý šok $\varepsilon_t$ navždy změní úroveň budoucí trajektorie procesu.
* **Rozptyl:** Exploduje v čase do nekonečna ($Var(y_t) \to \infty$ pro $t \to \infty$).

---

## 5.2 Náhodná procházka (Random Walk)

### 5.2.1 Čistá náhodná procházka (bez driftu)
Uvažujme:
$$y_t = y_{t-1} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2), \quad t = 1, 2, \dots$$
s pevným počátečním stavem $y_0$ v čase $t = 0$.

#### 1. Rozvoj v čase:
$$y_1 = y_0 + \varepsilon_1$$
$$y_2 = y_1 + \varepsilon_2 = y_0 + \varepsilon_1 + \varepsilon_2$$
$$y_t = y_0 + \sum_{i=1}^t \varepsilon_i$$

#### 2. Střední hodnota:
$$E[y_t] = E\left[y_0 + \sum_{i=1}^t \varepsilon_i\right] = y_0 + \sum_{i=1}^t E[\varepsilon_i] = y_0 = \text{konst.}$$

#### 3. Rozptyl:
$$Var(y_t) = Var\left(y_0 + \sum_{i=1}^t \varepsilon_i\right) = \sum_{i=1}^t Var(\varepsilon_i) = t \cdot \sigma^2$$
Rozptyl **lineárně roste s časem $t$**. 
$$\lim_{t \to \infty} Var(y_t) = \infty$$
Proces je nestacionární ve druhém momentu.

#### 4. Autokovariance a Autokorelace:
Nechť $t > s$:
$$Cov(y_t, y_s) = Cov\left( y_0 + \sum_{i=1}^t \varepsilon_i, \; y_0 + \sum_{j=1}^s \varepsilon_j \right) = \sum_{i=1}^t \sum_{j=1}^s Cov(\varepsilon_i, \varepsilon_j)$$
Protože $Cov(\varepsilon_i, \varepsilon_j) = \sigma^2$ pouze pro $i = j$ a jinak nula, a jelikož $s < t$, je počet shodných indexů roven přesně $s$:
$$Cov(y_t, y_s) = s \cdot \sigma^2 = \min(t, s) \cdot \sigma^2$$
Autokovariance závisí explicitně na časech $t$ a $s$, nikoliv pouze na jejich rozdílu $t - s$.

Autokorelační funkce pro $t$ a zpoždění $k$ ($s = t - k$):
$$\rho(t, t-k) = \frac{Cov(y_t, y_{t-k})}{\sqrt{Var(y_t) Var(y_{t-k})}} = \frac{(t-k)\sigma^2}{\sqrt{t\sigma^2 \cdot (t-k)\sigma^2}} = \frac{t-k}{\sqrt{t(t-k)}} = \sqrt{\frac{t-k}{t}} = \sqrt{1 - \frac{k}{t}}$$
Pro pevné zpoždění $k$ a čas jdoucí do nekonečna ($t \to \infty$):
$$\lim_{t \to \infty} \rho(t, t-k) = \lim_{t \to \infty} \sqrt{1 - \frac{k}{t}} = 1$$
Výběrová ACF nestacionárního procesu s jednotkovým kořenem zůstává extrémně blízko 1 i pro velmi vysoká zpoždění $k$ a klesá lineárně, nikoli exponenciálně.

---

### 5.2.2 Náhodná procházka s driftem
Přidáme-li nenulovou konstantu $\beta \neq 0$ (drift):
$$y_t = \beta + y_{t-1} + \varepsilon_t$$
Rozvinutím od $y_0$:
$$y_t = y_0 + \beta t + \sum_{i=1}^t \varepsilon_i$$
* **Střední hodnota:** $E[y_t] = y_0 + \beta t$ – má deterministický lineární trend!
* **Rozptyl:** $Var(y_t) = t \sigma^2$ – nestacionární stochastický rozptyl.
Proces v sobě kombinuje jak deterministický trend ($\beta t$), tak stochastický trend ($\sum \varepsilon_i$).

---

## 5.3 Modely ARIMA($p,d,q$)

> [!definition] Integrovaný proces řádu $d$ – $I(d)$
> Časovou řadu $\{y_t\}$ nazýváme **integrovanou řádu $d$**, značíme $y_t \sim I(d)$, jestliže se proces stane slabě stacionárním (a invertibilním) po $d$-násobném diferencování, přičemž po $(d-1)$ diferencováních stacionární nebyl:
> $$(1 - L)^d y_t \sim I(0)$$
> Proces $I(0)$ označuje slabě stacionární proces s kauzální a invertibilní ARMA reprezentací.

> [!definition] Autoregresní integrovaný model klouzavých průměrů – ARIMA($p,d,q$)
> Nechť $d \in \mathbb{N}_0$. Proces $\{y_t\}$ je ARIMA($p,d,q$), jestliže diferencovaná řada $w_t = \Delta^d y_t = (1 - L)^d y_t$ se řídí stacionárním a invertibilním procesem ARMA($p,q$):
> $$\Phi(L)(1 - L)^d y_t = c + \Theta(L)\varepsilon_t$$
> neboli:
> $$\left(1 - \sum_{i=1}^p \phi_i L^i\right) (1 - L)^d y_t = c + \left(1 + \sum_{j=1}^q \theta_j L^j\right)\varepsilon_t$$

---

## 5.4 Testy jednotkového kořene: Dickey-Fuller a ADF

Abychom objektivně rozhodli mezi $y_t \sim I(0)$ (TSP nebo stacionární AR) a $y_t \sim I(1)$ (DSP), používáme formální statistické testy.

### 5.4.1 Dickey-Fullerův test (DF)
Uvažujme AR(1):
$$y_t = \phi y_{t-1} + \varepsilon_t$$
Chceme testovat:
* $H_0: \phi = 1$ (proces má jednotkový kořen, je nestacionární $I(1)$)
* $H_1: |\phi| < 1$ (proces je stacionární $I(0)$)

Odečteme $y_{t-1}$ od obou stran rovnice:
$$y_t - y_{t-1} = (\phi - 1) y_{t-1} + \varepsilon_t \implies \Delta y_t = \gamma y_{t-1} + \varepsilon_t$$
kde $\gamma = \phi - 1$.
Nyní testujeme ekvivalentní hypotézu:
* $H_0: \gamma = 0$
* $H_1: \gamma < 0$ (jednostranný test)

Testová statistika je standardní $t$-poměr odhadnutý metodou nejmenších čtverců (OLS):
$$t_\gamma = \frac{\hat{\gamma}}{SE(\hat{\gamma})}$$

> [!danger] Zásadní úskalí: Proč nelze použít Studentovo $t$-rozdělení?
> Za platnosti nulové hypotézy ($H_0: \gamma = 0$) je proces nestacionární ($y_t$ je náhodná procházka). 
> Standardní asymptotická teorie centrální limitní věty selhává. Standardní odhad $\hat{\gamma}$ nekonverguje rychlostí $\sqrt{T}$ k normálnímu rozdělení, nýbrž rychlostí $T$ (tzv. **superkonvergence**).
> 
> Asymptotické rozdělení testové statistiky $t_\gamma$ není Studentovo $t$, nýbrž funkcionál Wienerova procesu (standardního Brownova pohybu $W(r)$):
> $$t_\gamma \xrightarrow{d} \frac{\frac{1}{2}\big( [W(1)]^2 - 1 \big)}{\sqrt{\int_0^1 [W(r)]^2 dr}}$$
> Toto rozdělení se nazývá **Dickey-Fullerovo rozdělení**. Je asymetrické a silně posunuté doleva. Použití tabulek běžného Studentova rozdělení (kde je 5% kritická hodnota jednostranného testu cca $-1.65$) by vedlo k masivnímu falešnému zamítání nulové hypotézy (kritická hodnota DF bez konstanty je přibližně $-1.95$).

---

### 5.4.2 Rozšířený Dickey-Fullerův test (ADF – Augmented Dickey-Fuller)
Pokud mají rezidua $\varepsilon_t$ v jednoduchém DF testu autokorelaci, standardní DF test ztrácí platnost. ADF test tento problém řeší přidáním $k$ zpožděných diferencí $\Delta y_{t-j}$ na pravou stranu rovnice, což absorbuje autokorelaci v chybovém členu.

Rozlišujeme 3 deterministické specifikace ADF testu:

1. **Model bez konstanty (No constant, no trend):**
   $$\Delta y_t = \gamma y_{t-1} + \sum_{j=1}^k \beta_j \Delta y_{t-j} + \varepsilon_t$$
2. **Model s konstantou (With drift / intercept):**
   $$\Delta y_t = \alpha + \gamma y_{t-1} + \sum_{j=1}^k \beta_j \Delta y_{t-j} + \varepsilon_t$$
3. **Model s konstantou a lineárním časovým trendem (With drift and deterministic trend):**
   $$\Delta y_t = \alpha + \delta t + \gamma y_{t-1} + \sum_{j=1}^k \beta_j \Delta y_{t-j} + \varepsilon_t$$

> [!warning] Kritické hodnoty závisejí na deterministické složce!
> Zahrnutí konstanty $\alpha$ a trendu $\delta t$ mění asymptotické rozdělení testové statistiky. 
> 
> Pro $5\%$ hladinu významnosti jsou asymptotické kritické hodnoty pro zamítnutí $H_0: \gamma = 0$:
> * Bez konstanty: $t_{crit} \approx -1.95$
> * S konstantou: $t_{crit} \approx -2.86$
> * S konstantou a trendem: $t_{crit} \approx -3.41$
> 
> **Pravidlo zamítnutí:** $H_0$ (jednotkový kořen) zamítáme na dané hladině $\alpha$ pouze tehdy, je-li vypočtená statistika **více záporná** než kritická hodnota:
> $$t_{ADF} < t_{crit} \implies \text{Zamítáme } H_0 \implies \text{Proces je stacionární}$$

---

# Přednáška 6: Box-Jenkinsova metodologie v praxi

Box-Jenkinsova metodologie (1970) je iterativní inženýrsko-statistický proces konstrukce modelu časové řady sestávající ze čtyř kroků:

```
┌────────────────────────────────────────────────────────┐
│                   1. IDENTIFIKACE                      │
│ - Ověření stacionarity (graf, ADF test) $\to$ volba $d$│
│ - Analýza ACF a PACF $\to$ předběžná volba $p$ a $q$   │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│                      2. ODHAD                          │
│ - Nelineární nejmenší čtverce (NLS)                    │
│ - Metoda maximální věrohodnosti (MLE: Conditional/Exact│
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│              3. DIAGNOSTICKÁ KONTROLA                  │
│ - Testy reziduí (bílý šum? Ljung-Box test)             │
│ - Testy invertibility a kauzality kořenů               │
│ - Informační kritéria (AIC, BIC)                       │
└─────────────┬────────────────────────────▲─────────────┘
              │ Vyhovuje?                  │
      NE ─────┴────────────────────────────┘ (Úprava specifikace)
      │
      │ ANO
┌─────▼──────────────────────────────────────────────────┐
│              4. FORECASTING (PREDIKCE)                 │
│ - Bodové a intervalové predikce v čase $T + h$         │
└────────────────────────────────────────────────────────┘
```

---

## 6.1 Metody odhadu parametrů

Na rozdíl od čistých AR($p$) modelů, které lze odhadnout lineární metodou nejmenších čtverců (OLS), obsahují modely s MA složkou nepozorované náhodné šoky $\varepsilon_t$. To vyžaduje nelineární optimalizační algoritmy.

### Metoda maximální věrohodnosti (Maximum Likelihood Estimation – MLE)
Předpokládejme Gaussovský proces: $\varepsilon_t \sim i.i.d. \, N(0, \sigma^2)$.
Mějme vektor pozorování $\mathbf{y} = (y_1, y_2, \dots, y_T)'$. Vektor neznámých parametrů je $\boldsymbol{\beta} = (\boldsymbol{\phi}', \boldsymbol{\theta}', \mu, \sigma^2)'$.

Sdružená hustota pravděpodobnosti (věrohodnostní funkce $L$):
$$L(\boldsymbol{\beta}; \mathbf{y}) = (2\pi)^{-\frac{T}{2}} |\boldsymbol{\Gamma}_T|^{-\frac{1}{2}} \exp\left( -\frac{1}{2} (\mathbf{y} - \boldsymbol{\mu})' \boldsymbol{\Gamma}_T^{-1} (\mathbf{y} - \boldsymbol{\mu}) \right)$$
kde $\boldsymbol{\Gamma}_T = E[(\mathbf{y} - \boldsymbol{\mu})(\mathbf{y} - \boldsymbol{\mu})']$ je kovarianční matice rozměru $T \times T$.

1. **Exaktní MLE (Exact MLE):** Využívá reprezentaci ve stavovém prostoru a **Kalmanův filtr** k exaktnímu výpočtu inverze $|\boldsymbol{\Gamma}_T|$ a kvadratické formy přes predikční chyby. Výpočetně náročné, ale přesné i pro malé výběry.
2. **Podmíněná MLE (Conditional MLE):** Položí počáteční nepozorované šoky rovny jejich nepodmíněné střední hodnotě: $\varepsilon_0 = \varepsilon_{-1} = \dots = \varepsilon_{1-q} = 0$ a $y_0 = y_{-1} = \dots = \mu$. Šoky se pak rekurzivně dopočítají dopředně. Pro velká $T$ asymptoticky ekvivalentní exaktní MLE.

---

## 6.2 Informační kritéria pro volbu modelu

Při volbě řádů $p$ a $q$ dochází ke kompromisu mezi těsností přilnutí k datům (goodness of fit) a parsimonií (úsporností počtu parametrů). Samotné maximalizování věrohodnosti $L$ vede k přeurčení (overfittingu). Proto penalizujeme logaritmus věrohodnosti $\ln L$ počtem odhadovaných parametrů $k = p + q + \text{intercept}$:

> [!definition] Akaikeho informační kritérium (AIC)
> $$\text{AIC} = -2 \ln(\hat{L}) + 2k$$
> Cílem je minimalizovat AIC. AIC je asymptoticky efektivní, ale pro konečné výběry má tendenci nadhodnocovat skutečný řád modelu (není konzistentní odhadce řádu).

> [!definition] Schwarzovo bayesovské informační kritérium (BIC / SBC)
> $$\text{BIC} = -2 \ln(\hat{L}) + k \cdot \ln(T)$$
> kde $T$ je počet pozorování. Protože pro $T \ge 8$ platí $\ln(T) > 2$, penalizuje BIC dodatečné parametry podstatně přísněji než AIC. 
> 
> BIC je **silně konzistentní** odhadce řádu: pokud data skutečně generoval konečný ARMA($p,q$), pak pro $T \to \infty$ pravděpodobnost volby správného řádu konverguje k 1.

---

## 6.3 Diagnostika reziduí: Ljung-Boxův test

Nechť $\hat{\varepsilon}_t$ jsou odhadnutá rezidua modelu:
$$\hat{\varepsilon}_t = y_t - \hat{y}_{t|t-1}$$
Pokud je model správně specifikován, musí vektor reziduí $\{\hat{\varepsilon}_t\}$ tvořit **bílý šum**. Nesmí v něm zůstat žádná autokorelace.

> [!definition] Ljung-Boxův test (Portmanteau test)
> Testuje sdruženou nulovou hypotézu, že prvních $m$ autokorelací reziduí je současně nulových:
> * $H_0: \rho_1(\hat{\varepsilon}) = \rho_2(\hat{\varepsilon}) = \dots = \rho_m(\hat{\varepsilon}) = 0$
> * $H_1: \exists j \in \{1, \dots, m\}: \rho_j(\hat{\varepsilon}) \neq 0$

Testová statistika:
$$Q_{LB} = T(T + 2) \sum_{k=1}^m \frac{\hat{\rho}_k^2(\hat{\varepsilon})}{T - k}$$
kde $\hat{\rho}_k(\hat{\varepsilon}) = \frac{\sum_{t=k+1}^T \hat{\varepsilon}_t \hat{\varepsilon}_{t-k}}{\sum_{t=1}^T \hat{\varepsilon}_t^2}$ je výběrová autokorelace reziduí.

> [!theorem] Asymptotické rozdělení $Q_{LB}$
> Pokud byla rezidua získána z odhadnutého modelu ARMA($p,q$), má statistika $Q_{LB}$ za platnosti $H_0$ asymptoticky chí-kvadrát rozdělení s počtem stupňů volnosti rovným **počtu testovaných autokorelací zmenšenému o počet odhadnutých ARMA parametrů**:
> $$Q_{LB} \xrightarrow{d} \chi^2(m - p - q)$$

> [!tip] Pravidlo rozhodování
> Pokud je $p$-hodnota testu **menší než zvolená hladina** (např. $p < 0.05$), **zamítáme $H_0$**. V reziduích stále zůstává systematická struktura a model je **špatně specifikován** (je nutné zvýšit $p$ nebo $q$). 
> Žádoucí výsledek je $p > 0.05$ (nezamítáme hypotézu bílého šumu).

---

## 6.4 Přehled nejčastějších chyb a úskalí v praxi

> [!danger] Seznam častých chyb (Common Pitfalls)
> 1. **Přestřelení diferencování (Over-differencing):**
>    Aplikace diferencování na již stacionární řadu $I(0)$.
>    *Důsledek:* Vytvoření umělého jednotkového kořene v MA složce. Pokud je $y_t = \varepsilon_t$ (bílý šum) a my spočteme $\Delta y_t = \varepsilon_t - \varepsilon_{t-1}$, vygenerovali jsme neinvertibilní MA(1) proces s $\theta = -1$. To vede k destrukci informace na nízkých frekvencích a problémům při odhadu.
>
> 2. **Záměna významu t-statistiky v ADF testu:**
>    Srovnávání $t$-statistiky ADF testu s hodnotou $-1.96$ ze Studentova rozdělení místo s tabulkami Dickey-Fullerova rozdělení. Vede k chybnému závěru, že proces je stacionární, ačkoliv má jednotkový kořen.
>
> 3. **Ignorování stupňů volnosti v Ljung-Boxově testu:**
>    Nastavení stupňů volnosti testu na $m$ místo na $m - p - q$. Standardní statistické balíčky testující obecné řady berou $\chi^2(m)$. Pokud testujeme rezidua po odhadu ARMA, musíme stupně volnosti manuálně korigovat, jinak je test příliš konzervativní.
>
> 4. **Multikolinearita parametrů při společných kořenech:**
>    Navržení modelu ARMA(2,2) na data, která by vyhovovala ARMA(1,1). Odhady koeficientů budou mít masivní standardní chyby a t-testy budou tvrdit, že žádný parametr není významný, ačkoliv model jako celek vysvětluje variabilitu výborně. Vždy zkoumejte, zda se kořeny $\Phi(z) = 0$ a $\Theta(z) = 0$ vzájemně numericky neblíží.
>
> 5. **Opačná konvence znamének u parametrů:**
>    V literatuře a software existují dvě odlišné konvence zápisu:
>    * Konvence A (Box-Jenkins / R `arima`): $X_t = \phi_1 X_{t-1} + \varepsilon_t + \theta_1 \varepsilon_{t-1}$
>    * Konvence B (ekonometrická / SAS / MATLAB / Python `statsmodels`): $X_t = \phi_1 X_{t-1} + \varepsilon_t - \theta_1 \varepsilon_{t-1}$
>    Před interpretací odhadnutých parametrů $\theta$ vždy zkontrolujte softwarovou definici znaménka!

---

## 6.5 Komplexní aplikační případová studie: Modelování inflace krok za krokem

Tato případová studie demonstruje kompletní analytický cyklus na reálných datech: kvartální míře inflace (anualizovaná procentní změna deflátoru HDP, $T = 160$ pozorování).

### Krok 1: Vizuální inspekce a test stacionarity
Původní řada $y_t$ osciluje kolem kladné střední hodnoty, ale vykazuje silnou perzistenci (pomalý návrat ke střední hodnotě).

Provedeme **ADF test s konstantou** na hladině $\alpha = 0.05$:
* Odhadnutá rovnice: $\Delta y_t = \alpha + \gamma y_{t-1} + \sum_{j=1}^2 \beta_j \Delta y_{t-j} + e_t$
* Vypočtená testová statistika: $t_{ADF} = -2.14$
* Kritická hodnota (ADF s konstantou, $T=160$): $t_{crit} = -2.88$

**Vyhodnocení:**
Jelikož $-2.14 > -2.88$, testová statistika neleží v kritickém oboru (není více záporná než kritická hodnota). **Nezamítáme $H_0$**. Řada má jednotkový kořen, $y_t \sim I(1)$.

Aplikujeme první diferenci: $w_t = \Delta y_t = y_t - y_{t-1}$.
Opakujeme ADF test na diferencované řadě $w_t$ (model bez konstanty):
* $t_{ADF} = -7.42$
* Kritická hodnota: $t_{crit} = -1.95$

**Vyhodnocení:**
Jelikož $-7.42 < -1.95$, **zamítáme $H_0$**. Diferencovaná řada $w_t$ je stacionární, $w_t \sim I(0)$. Budeme tedy odhadovat model **ARIMA($p, 1, q$)**.

---

### Krok 2: Identifikace řádů $p$ a $q$
Vypočítáme výběrovou ACF a PACF pro diferencovanou řadu $w_t$:

```
Výběrová ACF pro wt:
Lag 1:  [██████████      ]  0.42  (statisticky významné, překračuje pásmo spolehlivosti ±0.155)
Lag 2:  [███             ]  0.18  (významné)
Lag 3:  [█               ]  0.06  (nevýznamné)
Lag 4:  [                ] -0.02  (nevýznamné)

Výběrová PACF pro wt:
Lag 1:  [██████████      ]  0.42  (významné)
Lag 2:  [                ]  0.01  (nevýznamné)
Lag 3:  [                ] -0.04  (nevýznamné)
Lag 4:  [                ]  0.02  (nevýznamné)
```

**Analýza diagnostického profilu:**
* PACF má významný pouze lag 1 a na lagu 2 se prudce usekne na nulu ($\alpha_1 \neq 0, \alpha_k \approx 0$ pro $k \ge 2$).
* ACF exponenciálně klesá ($0.42 \to 0.18 \to 0.06 \approx 0.42^k$).

Tento profil jednoznačně indikuje proces **AR(1) pro diference**, což odpovídá celkovému modelu **ARIMA(1, 1, 0)**. Jako alternativního kandidáta pro srovnání navrhneme smíšený model **ARIMA(1, 1, 1)** a model **ARIMA(0, 1, 1)**.

---

### Krok 3: Odhad a volba modelu pomocí informačních kritérií
Odhadneme všechny tři kandidátní modely metodou maximální věrohodnosti (Exact MLE):

| Model | Odhadnuté parametry | Log-věrohodnost ($\ln \hat{L}$) | AIC | BIC |
| :--- | :--- | :--- | :--- | :--- |
| **ARIMA(0, 1, 1)** | $\hat{\theta}_1 = 0.385 \; (SE = 0.072)$ | $-214.30$ | $432.60$ | $438.75$ |
| **ARIMA(1, 1, 0)** | $\hat{\phi}_1 = 0.418 \; (SE = 0.071)$ | $-210.15$ | **424.30** | **430.45** |
| **ARIMA(1, 1, 1)** | $\hat{\phi}_1 = 0.350, \; \hat{\theta}_1 = 0.092$ | $-209.85$ | $425.70$ | $434.93$ |

**Hodnocení:**
1. Model ARIMA(1, 1, 0) má nejnižší hodnotu AIC (424.30) i BIC (430.45).
2. U modelu ARIMA(1, 1, 1) je parametr $\hat{\theta}_1 = 0.092$ statisticky nevýznamný ($p$-hodnota $= 0.41$), model trpí mírnou redundancí parametrů.
3. Vítězným modelem je **ARIMA(1, 1, 0)**.

Odhadnutá rovnice:
$$(1 - 0.418 L) (1 - L) y_t = \varepsilon_t, \quad \hat{\sigma}^2 = 0.842$$
neboli:
$$\Delta y_t = 0.418 \Delta y_{t-1} + \varepsilon_t$$
$$y_t = 1.418 y_{t-1} - 0.418 y_{t-2} + \varepsilon_t$$

Ověření kauzality:
Kořen rovnice $1 - 0.418 z = 0$ je $z_0 = \frac{1}{0.418} \approx 2.39 > 1$. Model je bezpečně kauzální.

---

### Krok 4: Diagnostika reziduí vybraného modelu ARIMA(1, 1, 0)
Získáme řadu reziduí $\hat{\varepsilon}_t$.

1. **Ljung-Boxův test autokorelace reziduí:**
   Testujeme pro $m = 12$ zpoždění.
   Počet odhadnutých parametrů je $p + q = 1 + 0 = 1$.
   Stupně volnosti: $df = m - (p + q) = 12 - 1 = 11$.
   * Vypočtená hodnota statistiky: $Q_{LB} = 9.85$
   * Kritická hodnota $\chi^2_{0.95}(11) = 19.68$
   * Odpovídající $p$-hodnota: $P(\chi^2(11) > 9.85) = 0.544$

   **Závěr testu:** Jelikož $p = 0.544 > 0.05$, **nezamítáme nulovou hypotézu**. Rezidua vykazují chování bílého šumu. V modelu nezůstala žádná nevysvětlená autokorelační struktura.

2. **Test normality reziduí (Jarque-Bera test):**
   * Šikmost reziduí: $S = 0.12$
   * Špičatost reziduí: $K = 3.15$
   * Jarque-Bera statistika: $JB = \frac{T}{6}\left(S^2 + \frac{(K-3)^2}{4}\right) = \frac{159}{6}\left(0.0144 + \frac{0.0225}{4}\right) \approx 0.53$
   * $p$-hodnota: $0.767$
   
   Rezidua jsou v souladu s normálním rozdělením $N(0, \sigma^2)$, předpoklad Gaussovského procesu pro konstrukci intervalů spolehlivosti predikce je validní.

---

### Krok 5: Výpočet bodové a intervalové predikce

Mějme poslední dvě pozorování: $y_{T-1} = 2.8\,\%$, $y_T = 3.2\,\%$.
Předpovídáme budoucí hodnoty na horizont $h = 1$ a $h = 2$ kvartály dopředu.

#### Predikce na 1 krok dopředu ($T+1$):
Model v úrovních: $y_{T+1} = 1.418 y_T - 0.418 y_{T-1} + \varepsilon_{T+1}$.
Podmíněná střední hodnota (optimální bodová predikce v $L^2$ smyslu):
$$\hat{y}_{T+1|T} = E[y_{T+1} \mid \mathcal{F}_T] = 1.418 y_T - 0.418 y_{T-1} + E[\varepsilon_{T+1} \mid \mathcal{F}_T]$$
Protože $\varepsilon_{T+1}$ je budoucí šok, je $E[\varepsilon_{T+1} \mid \mathcal{F}_T] = 0$:
$$\hat{y}_{T+1|T} = 1.418 (3.2) - 0.418 (2.8) = 4.5376 - 1.1704 = 3.3672\,\%$$

Chyba predikce na 1 krok:
$$e_{T+1|T} = y_{T+1} - \hat{y}_{T+1|T} = \varepsilon_{T+1}$$
Rozptyl chyby predikce:
$$Var(e_{T+1|T}) = Var(\varepsilon_{T+1}) = \sigma^2 = 0.842$$
Směrodatná odchylka predikce:
$$SE_1 = \sqrt{0.842} \approx 0.9176$$

95% interval spolehlivosti predikce:
$$\hat{y}_{T+1|T} \pm 1.96 \cdot SE_1 = 3.3672 \pm 1.96 \cdot 0.9176 = 3.3672 \pm 1.7985 \implies [1.57\,\%, \; 5.17\,\%]$$

#### Predikce na 2 kroky dopředu ($T+2$):
Model: $y_{T+2} = 1.418 y_{T+1} - 0.418 y_T + \varepsilon_{T+2}$.
Podmíněná střední hodnota:
$$\hat{y}_{T+2|T} = 1.418 \hat{y}_{T+1|T} - 0.418 y_T + 0$$
Dosadíme již vypočtenou predikci $\hat{y}_{T+1|T}$:
$$\hat{y}_{T+2|T} = 1.418 (3.3672) - 0.418 (3.2) = 4.7747 - 1.3376 = 3.4371\,\%$$

Chyba predikce na 2 kroky:
$$y_{T+2} = 1.418 (\hat{y}_{T+1|T} + \varepsilon_{T+1}) - 0.418 y_T + \varepsilon_{T+2}$$
$$e_{T+2|T} = y_{T+2} - \hat{y}_{T+2|T} = 1.418 \varepsilon_{T+1} + \varepsilon_{T+2}$$
Rozptyl chyby predikce:
$$Var(e_{T+2|T}) = Var(1.418 \varepsilon_{T+1} + \varepsilon_{T+2}) = 1.418^2 \sigma^2 + \sigma^2 = \sigma^2 (1 + 1.418^2) = 0.842 \cdot (1 + 2.0107) = 0.842 \cdot 3.0107 \approx 2.535$$
Směrodatná odchylka predikce na horizont 2:
$$SE_2 = \sqrt{2.535} \approx 1.5922$$
d
95% interval spolehlivosti:
$$\hat{y}_{T+2|T} \pm 1.96 \cdot SE_2 = 3.4371 \pm 1.96 \cdot 1.5922 = 3.4371 \pm 3.1207 \implies [0.32\,\%, \; 6.56\,\%]$$

> [!summary] Závěrečný poznatek k predikcím ARIMA procesů
> Všimněte si, jak se s rostoucím horizontem predikce $h$ rozptyl chyby predikce prudce zvyšuje ($SE_1 = 0.92 \to SE_2 = 1.59$). 
> U stacionárního procesu $I(0)$ by rozptyl predikční chyby pro $h \to \infty$ konvergoval ke konečnému nepodmíněnému rozptylu $\gamma_0$. U procesů integrovaných $I(1)$ rozptyl chyby predikce roste **nade všechny meze lineárně s horizontem predikce** ($Var(e_{T+h|T}) \propto h$). 
> Z toho důvodu jsou dlouhodobé intervalové predikce u nestacionárních časových řad extrémně široké, což přesně odráží permanentní charakter budoucích kumulovaných stochastických šoků.