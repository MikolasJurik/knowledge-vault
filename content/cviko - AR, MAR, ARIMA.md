# Univariátní modely časových řad (Teorie a sbírka úloh)

---

# ČÁST I: Komplexní studijní text

---

## 1. Algebra operátoru zpoždění a diferencování

Základním matematickým nástrojem pro manipulaci s časovými řadami je **operátor zpoždění** (lag operator, označovaný $L$ nebo $B$ jako *backward shift operator*).

### 1.1 Definice a elementární vlastnosti

Nechť $\{y_t\}_{t=-\infty}^{\infty}$ je diskrétní stochastický proces. Operátor zpoždění $L$ je lineární zobrazení definované předpisem:
$$L y_t = y_{t-1}$$

Opakovanou aplikací operátoru zpoždění pro libovolné celé číslo $k \ge 1$ získáme:
$$L^k y_t = L(L^{k-1} y_t) = y_{t-k}$$

Z definice plyne:
* **Identita:** $L^0 y_t = 1 \cdot y_t = y_t$
* **Inverzní operátor (operátor předstihu):** $L^{-1} y_t = y_{t+1}$
* **Aplikace na konstantu:** Pro libovolnou deterministickou konstantu $c \in \mathbb{R}$ platí $L c = c$.

### 1.2 Polynomy v operátoru zpoždění
Mějme reálná čísla $\alpha_0, \alpha_1, \dots, \alpha_p$. Definujeme polynom $\alpha(L)$ stupně $p$:
$$\alpha(L) = \alpha_0 + \alpha_1 L + \alpha_2 L^2 + \dots + \alpha_p L^p = \sum_{j=0}^{p} \alpha_j L^j$$
Působení tohoto polynomu na proces $y_t$ je definováno lineární kombinací:
$$\alpha(L) y_t = \sum_{j=0}^{p} \alpha_j L^j y_t = \sum_{j=0}^{p} \alpha_j y_{t-j}$$

### 1.3 Inverze polynomu v operátoru zpoždění
Uvažujme nejjednodušší monický polynom prvního stupně: $A(L) = 1 - \phi L$. Hledáme formální mocninnou řadu $A(L)^{-1} = \sum_{j=0}^{\infty} \psi_j L^j$ takovou, že:
$$(1 - \phi L) \left( \sum_{j=0}^{\infty} \psi_j L^j \right) = 1$$

Roznásobením levé strany dostáváme:
$$\sum_{j=0}^{\infty} \psi_j L^j - \sum_{j=0}^{\infty} \phi \psi_j L^{j+1} = 1$$
$$\psi_0 + \sum_{j=1}^{\infty} (\psi_j - \phi \psi_{j-1}) L^j = 1$$

Porovnáním koeficientů u jednotlivých mocnin $L^j$:
* $L^0: \psi_0 = 1$
* $L^1: \psi_1 - \phi \psi_0 = 0 \implies \psi_1 = \phi$
* $L^2: \psi_2 - \phi \psi_1 = 0 \implies \psi_2 = \phi^2$
* Obecně pro $j \ge 1: \psi_j = \phi^j$

Formální inverze má tvar:
$$(1 - \phi L)^{-1} = \sum_{j=0}^{\infty} \phi^j L^j$$

#### Podmínka konvergence
Aby tato nekonečná řada operátorů aplikovaná na stochastický proces $\varepsilon_t$ měla smysl v prostoru náhodných veličin s konečným rozptylem (konvergence ve středním kvadratickém), musí být posloupnost koeficientů absolutně sumovatelná:
$$\sum_{j=0}^{\infty} |\phi^j| < \infty \iff |\phi| < 1$$

> [!warning] Past: Formální vs. konvergentní inverze
> Pokud $|\phi| \ge 1$, algebraický výraz $\sum_{j=0}^{\infty} \phi^j L^j$ existuje pouze formálně, ale aplikovaný na data dává proces s nekonečným rozptylem. Pro $|\phi| > 1$ existuje konvergentní inverze pouze v budoucích hodnotách (tzv. neakauzální reprezentace):
> $$(1 - \phi L) = -\phi L \left(1 - \frac{1}{\phi} L^{-1}\right) \implies (1 - \phi L)^{-1} = -\sum_{j=1}^{\infty} \phi^{-j} L^{-j}$$

### 1.4 Operátor diferencování
Diferenční operátor $\Delta$ představuje první diferenci časové řady:
$$\Delta y_t = y_t - y_{t-1} = (1 - L)y_t$$

Diferenci $d$-tého řádu vyjadřujeme mocninou polynomu $(1 - L)$:
$$\Delta^d y_t = (1 - L)^d y_t$$

Podle binomické věty:
$$(1 - L)^d = \sum_{k=0}^{d} \binom{d}{k} (-1)^k L^k$$
* Pro $d = 1$: $\Delta y_t = y_t - y_{t-1}$
* Pro $d = 2$: $\Delta^2 y_t = (1 - 2L + L^2) y_t = y_t - 2y_{t-1} + y_{t-2}$

---

## 2. Autoregresní procesy — AR($p$)

### 2.1 Formální definice AR($p$)
Proces $\{y_t\}$ nazýváme **autoregresním procesem řádu $p$**, značeno AR($p$), pokud vyhovuje diferenční rovnici:
$$y_t = c + \phi_1 y_{t-1} + \phi_2 y_{t-2} + \dots + \phi_p y_{t-p} + \varepsilon_t$$
kde:
* $c \in \mathbb{R}$ je konstanta (úroveň posunu),
* $\phi_1, \dots, \phi_p \in \mathbb{R}$ jsou autoregresní parametry, přičemž $\phi_p \neq 0$,
* $\{\varepsilon_t\}$ je bílý šum: $E[\varepsilon_t] = 0$, $Var(\varepsilon_t) = \sigma^2$ a $Cov(\varepsilon_t, \varepsilon_s) = 0$ pro všechna $t \neq s$.

Zápis pomocí polynomu zpoždění:
$$\Phi(L) y_t = c + \varepsilon_t, \quad \text{kde } \Phi(L) = 1 - \sum_{j=1}^{p} \phi_j L^j$$

### 2.2 Podmínka stacionarity a kauzality
Proces AR($p$) je **kovariančně stacionární a kauzální** (lze jej vyjádřit jako lineární funkci současných a minulých šoků $\varepsilon_t, \varepsilon_{t-1}, \dots$), právě když všechny kořeny charakteristického polynomu:
$$\Phi(z) = 1 - \phi_1 z - \phi_2 z^2 - \dots - \phi_p z^p = 0$$
leží **vně jednotkového kruhu v komplexní rovině**, tj. pro všechny kořeny $z_i \in \mathbb{C}$ platí:
$$|z_i| > 1, \quad \forall i = 1, \dots, p$$

> [!note] Pozorování: Různé konvence charakteristických rovnic
> V literatuře existují dva ekvivalentní zápisy:
> 1. **Lag polynomiální zápis:** $1 - \sum_{j=1}^p \phi_j z^j = 0 \implies$ kořeny musí ležet **VNĚ** jednotkového kruhu ($|z| > 1$).
> 2. **Klasický algebraický zápis diferenčních rovnic:** $\lambda^p - \sum_{j=1}^p \phi_j \lambda^{p-j} = 0 \implies$ kořeny (vlastní čísla matice přechodu) musí ležet **UVNITŘ** jednotkového kruhu ($|\lambda| < 1$).
> Vztah mezi kořeny je jednoduše $z = \frac{1}{\lambda}$.

---

### 2.3 Detailní rozbor procesu AR(1)

Mějme rovnici:
$$y_t = c + \phi y_{t-1} + \varepsilon_t$$

#### Krok 1: Podmínka stacionarity
Charakteristická rovnice:
$$1 - \phi z = 0 \implies z = \frac{1}{\phi}$$
Podmínka $|z| > 1$ dává:
$$\left|\frac{1}{\phi}\right| > 1 \iff |\phi| < 1$$

#### Krok 2: Odvození kauzální reprezentace (Woldova dekompozice)
Předpokládejme $|\phi| < 1$. Aplikujeme inverzní operátor $(1 - \phi L)^{-1}$:
$$y_t = (1 - \phi L)^{-1} c + (1 - \phi L)^{-1} \varepsilon_t$$
Protože $(1 - \phi L)^{-1} = \sum_{j=0}^{\infty} \phi^j L^j$:
$$y_t = \sum_{j=0}^{\infty} \phi^j c + \sum_{j=0}^{\infty} \phi^j \varepsilon_{t-j}$$
Součet geometrické řady konstant dává $\sum_{j=0}^{\infty} \phi^j c = \frac{c}{1 - \phi}$. Tedy:
$$y_t = \frac{c}{1 - \phi} + \sum_{j=0}^{\infty} \phi^j \varepsilon_{t-j}$$

#### Krok 3: Střední hodnota
Aplikací operátoru střední hodnoty $E[\cdot]$:
$$E[y_t] = E\left[\frac{c}{1 - \phi}\right] + \sum_{j=0}^{\infty} \phi^j E[\varepsilon_{t-j}]$$
Protože $E[\varepsilon_{t-j}] = 0$ pro všechna $j$:
$$\mu = E[y_t] = \frac{c}{1 - \phi}$$

Alternativní odvození za předpokladu stacionarity ($E[y_t] = E[y_{t-1}] = \mu$):
$$E[y_t] = c + \phi E[y_{t-1}] + E[\varepsilon_t] \implies \mu = c + \phi \mu \implies \mu(1 - \phi) = c \implies \mu = \frac{c}{1 - \phi}$$

#### Krok 4: Rozptyl ($\gamma_0$)
Definujme centrovanou veličinu $\tilde{y}_t = y_t - \mu$.
$$y_t - \mu = \phi (y_{t-1} - \mu) + \varepsilon_t \implies \tilde{y}_t = \phi \tilde{y}_{t-1} + \varepsilon_t$$
Umocněním obou stran na druhou:
$$\tilde{y}_t^2 = \phi^2 \tilde{y}_{t-1}^2 + 2 \phi \tilde{y}_{t-1} \varepsilon_t + \varepsilon_t^2$$
Aplikujeme střední hodnotu:
$$E[\tilde{y}_t^2] = \phi^2 E[\tilde{y}_{t-1}^2] + 2\phi E[\tilde{y}_{t-1} \varepsilon_t] + E[\varepsilon_t^2]$$
Víme, že:
1. Ze stacionarity: $Var(y_t) = Var(y_{t-1}) = \gamma_0 \implies E[\tilde{y}_t^2] = E[\tilde{y}_{t-1}^2] = \gamma_0$.
2. Z kauzality: $\tilde{y}_{t-1} = \sum_{j=0}^{\infty} \phi^j \varepsilon_{t-1-j}$, což závisí pouze na šocích do času $t-1$. Proto $E[\tilde{y}_{t-1} \varepsilon_t] = 0$.
3. $E[\varepsilon_t^2] = \sigma^2$.

Dosadíme:
$$\gamma_0 = \phi^2 \gamma_0 + 0 + \sigma^2$$
$$\gamma_0 (1 - \phi^2) = \sigma^2$$
$$\gamma_0 = Var(y_t) = \frac{\sigma^2}{1 - \phi^2}$$

#### Krok 5: Autokovariance ($\gamma_k$) a Autokorelace ($\rho_k$)
Vynásobíme centrovanou rovnici $\tilde{y}_t = \phi \tilde{y}_{t-1} + \varepsilon_t$ veličinou $\tilde{y}_{t-k}$ pro $k \ge 1$:
$$\tilde{y}_t \tilde{y}_{t-k} = \phi \tilde{y}_{t-1} \tilde{y}_{t-k} + \varepsilon_t \tilde{y}_{t-k}$$
Aplikujeme střední hodnotu:
$$E[\tilde{y}_t \tilde{y}_{t-k}] = \phi E[\tilde{y}_{t-1} \tilde{y}_{t-k}] + E[\varepsilon_t \tilde{y}_{t-k}]$$
Z definice $\gamma_k = Cov(y_t, y_{t-k}) = E[\tilde{y}_t \tilde{y}_{t-k}]$ a faktu, že $E[\varepsilon_t \tilde{y}_{t-k}] = 0$ pro $k \ge 1$:
$$\gamma_k = \phi \gamma_{k-1}$$

Máme homogenní lineární diferenční rovnici prvního řádu:
$$\gamma_k = \phi \gamma_{k-1} = \phi(\phi \gamma_{k-2}) = \dots = \phi^k \gamma_0$$
Dosazením za $\gamma_0$:
$$\gamma_k = \frac{\sigma^2 \phi^k}{1 - \phi^2}, \quad k \ge 0$$

Autokorelační funkce (ACF) je definována jako $\rho_k = \frac{\gamma_k}{\gamma_0}$:
$$\rho_k = \frac{\phi^k \gamma_0}{\gamma_0} = \phi^k, \quad k \ge 0$$

---

### 2.4 Detailní rozbor procesu AR(2)

Uvažujme centrovaný proces AR(2):
$$y_t = \phi_1 y_{t-1} + \phi_2 y_{t-2} + \varepsilon_t$$

#### Podmínky stacionarity (Trojúhelník stacionarity)
Charakteristický polynom: $\Phi(z) = 1 - \phi_1 z - \phi_2 z^2 = 0$.
Kořeny $z_1, z_2$ musí splňovat $|z_1| > 1$ a $|z_2| > 1$. Požadavek na parametry vede k soustavě tří nerovností:
1. $\phi_1 + \phi_2 < 1$
2. $\phi_2 - \phi_1 < 1$
3. $|\phi_2| < 1$ (ekvivalentně $-1 < \phi_2 < 1$)

Graficky tyto nerovnosti vymezují v rovině $(\phi_1, \phi_2)$ trojúhelník.

```
                  phi_2
                    ^
                    | (0, 1)
                   /|\
                  / | \
                 /  |  \
  phi_2 - phi_1 = 1 /   |   \  phi_1 + phi_2 = 1
               /    |    \
              /     |     \
  (-2, -1)   /______|______\   (2, -1)
            -2     0|      2   ---> phi_1
                    |
                 (phi_2 = -1)
```

#### Yule-Walkerovy rovnice pro AR(2)
Vynásobíme rovnici procesu veličinou $y_{t-k}$ pro $k \ge 0$ a aplikujeme střední hodnotu ($E[y_t y_{t-k}] = \gamma_k$):
$$E[y_t y_{t-k}] = \phi_1 E[y_{t-1} y_{t-k}] + \phi_2 E[y_{t-2} y_{t-k}] + E[\varepsilon_t y_{t-k}]$$

* **Pro $k = 0$:**
  $$\gamma_0 = \phi_1 \gamma_1 + \phi_2 \gamma_2 + \sigma^2 \quad (\text{protože } E[\varepsilon_t y_t] = E[\varepsilon_t(\dots + \varepsilon_t)] = \sigma^2)$$
* **Pro $k = 1$:**
  $$\gamma_1 = \phi_1 \gamma_0 + \phi_2 \gamma_1 + 0 \implies \gamma_1 (1 - \phi_2) = \phi_1 \gamma_0 \implies \rho_1 = \frac{\phi_1}{1 - \phi_2}$$
* **Pro $k = 2$:**
  $$\gamma_2 = \phi_1 \gamma_1 + \phi_2 \gamma_0 \implies \rho_2 = \phi_1 \rho_1 + \phi_2 = \frac{\phi_1^2}{1 - \phi_2} + \phi_2 = \frac{\phi_1^2 + \phi_2 - \phi_2^2}{1 - \phi_2}$$
* **Pro obecné $k \ge 2$:**
  $$\rho_k = \phi_1 \rho_{k-1} + \phi_2 \rho_{k-2}$$

#### Dynamika ACF podle diskriminantu
Chování diferenční rovnice $\rho_k - \phi_1 \rho_{k-1} - \phi_2 \rho_{k-2} = 0$ závisí na diskriminantu $D = \phi_1^2 + 4\phi_2$:
* Pokud **$\phi_1^2 + 4\phi_2 \ge 0$**: Kořeny jsou reálné. ACF exponenciálně klesá (případně alternuje znaménko).
* Pokud **$\phi_1^2 + 4\phi_2 < 0$**: Kořeny jsou komplexně sdružené. ACF vykazuje **pseudocyklické chování** s tlumenou sinusoidou:
  $$\rho_k = R^k \frac{\sin(k\theta + \psi)}{\sin(\psi)}, \quad \text{kde } R = \sqrt{-\phi_2}, \quad \cos(\theta) = \frac{\phi_1}{2\sqrt{-\phi_2}}$$

---

### 2.5 Parciální autokorelační funkce (PACF)
Mějme autokorelace $\rho_1, \dots, \rho_k$. **Parciální autokorelace řádu $k$**, značená $\phi_{kk}$, měří korelaci mezi $y_t$ a $y_{t-k}$ po očištění o lineární vliv všech vložených zpoždění $y_{t-1}, y_{t-2}, \dots, y_{t-k+1}$.

Matematicky je $\phi_{kk}$ definována jako poslední koeficient v lineární regresi:
$$y_t = \phi_{k1} y_{t-1} + \phi_{k2} y_{t-2} + \dots + \phi_{kk} y_{t-k} + u_t$$

Pomocí Cramerova pravidla aplikovaného na Yule-Walkerův systém:
$$\begin{pmatrix} 1 & \rho_1 & \dots & \rho_{k-1} \\ \rho_1 & 1 & \dots & \rho_{k-2} \\ \vdots & \vdots & \ddots & \vdots \\ \rho_{k-1} & \rho_{k-2} & \dots & 1 \end{pmatrix} \begin{pmatrix} \phi_{k1} \\ \phi_{k2} \\ \vdots \\ \phi_{kk} \end{pmatrix} = \begin{pmatrix} \rho_1 \\ \rho_2 \\ \vdots \\ \rho_k \end{pmatrix}$$

$$\phi_{kk} = \frac{\det(P_k^*)}{\det(P_k)}$$
kde $P_k$ je Toeplitzova korelační matice řádu $k$ a $P_k^*$ je matice $P_k$, v níž je poslední sloupec nahrazen vektorem $(\rho_1, \rho_2, \dots, \rho_k)'$.

* **Pro $k = 1$:** $\phi_{11} = \rho_1$
* **Pro $k = 2$:**
  $$\phi_{22} = \frac{\det \begin{pmatrix} 1 & \rho_1 \\ \rho_1 & \rho_2 \end{pmatrix}}{\det \begin{pmatrix} 1 & \rho_1 \\ \rho_1 & 1 \end{pmatrix}} = \frac{\rho_2 - \rho_1^2}{1 - \rho_1^2}$$

#### Klíčová vlastnost pro AR($p$)
Pro proces AR($p$) platí:
$$\phi_{kk} = 0 \quad \forall k > p$$
PACF se **ostře usekne za zpožděním $p$**.

---

## 3. Procesy klouzavých průměrů — MA($q$)

### 3.1 Formální definice MA($q$)
Proces $\{y_t\}$ je **procesem klouzavých průměrů řádu $q$**, značeno MA($q$), pokud je dán lineární kombinací bílého šumu:
$$y_t = \mu + \varepsilon_t + \theta_1 \varepsilon_{t-1} + \theta_2 \varepsilon_{t-2} + \dots + \theta_q \varepsilon_{t-q}$$
kde:
* $\mu \in \mathbb{R}$ je nepodmíněná střední hodnota,
* $\theta_1, \dots, \theta_q \in \mathbb{R}$ jsou parametry, $\theta_q \neq 0$,
* $\varepsilon_t \sim WN(0, \sigma^2)$.

V operátorovém zápisu:
$$y_t = \mu + \Theta(L)\varepsilon_t, \quad \text{kde } \Theta(L) = 1 + \sum_{j=1}^{q} \theta_j L^j$$

### 3.2 Vlastnosti procesu MA($q$)
1. **Stacionarita:** Protože $y_t$ je konečnou lineární kombinací stacionárních náhodných veličin $\varepsilon_t$, je proces MA($q$) **vždy kovariančně stacionární**, bez ohledu na hodnoty koeficientů $\theta_j$.
2. **Střední hodnota:** $E[y_t] = \mu + \sum_{j=1}^q \theta_j E[\varepsilon_{t-j}] = \mu$.
3. **Rozptyl:**
   $$Var(y_t) = \gamma_0 = Var\left( \varepsilon_t + \sum_{j=1}^q \theta_j \varepsilon_{t-j} \right) = \sigma^2 \left(1 + \theta_1^2 + \theta_2^2 + \dots + \theta_q^2\right) = \sigma^2 \left(1 + \sum_{j=1}^q \theta_j^2\right)$$
4. **Autokovariance ($\gamma_k$):**
   $$\gamma_k = Cov(y_t, y_{t-k}) = E\left[ \left(\sum_{i=0}^q \theta_i \varepsilon_{t-i}\right) \left(\sum_{j=0}^q \theta_j \varepsilon_{t-k-j}\right) \right], \quad (\text{kde } \theta_0 = 1)$$
   Nenulové součiny vznikají pouze tehdy, když se indexy šoků shodují: $t - i = t - k - j \implies i = k + j$.
   * Pro $k \le q$:
     $$\gamma_k = \sigma^2 \sum_{j=0}^{q-k} \theta_j \theta_{j+k}$$
   * Pro $k > q$:
     $$\gamma_k = 0$$

> [!important] Klíčová vlastnost pro MA($q$)
> ACF procesu MA($q$) se **ostře usekne po $q$ zpožděních**.
> $$\rho_k = 0 \quad \forall k > q$$

---

### 3.3 Detailní rozbor procesu MA(1)

Mějme proces:
$$y_t = \mu + \varepsilon_t + \theta \varepsilon_{t-1}$$

#### Momenty:
* $\gamma_0 = \sigma^2 (1 + \theta^2)$
* $\gamma_1 = E[(\varepsilon_t + \theta \varepsilon_{t-1})(\varepsilon_{t-1} + \theta \varepsilon_{t-2})] = \theta E[\varepsilon_{t-1}^2] = \theta \sigma^2$
* $\gamma_k = 0$ pro všechna $k \ge 2$

Autokorelační funkce:
$$\rho_1 = \frac{\theta \sigma^2}{\sigma^2 (1 + \theta^2)} = \frac{\theta}{1 + \theta^2}, \quad \rho_k = 0 \quad \forall k \ge 2$$

#### Analýza funkce $\rho_1(\theta)$:
Hledejme extrémy funkce $f(\theta) = \frac{\theta}{1 + \theta^2}$:
$$f'(\theta) = \frac{1(1 + \theta^2) - \theta(2\theta)}{(1 + \theta^2)^2} = \frac{1 - \theta^2}{(1 + \theta^2)^2} = 0 \implies \theta = \pm 1$$
* Pro $\theta = 1$: $\rho_1 = \frac{1}{2} = 0.5$ (maximum)
* Pro $\theta = -1$: $\rho_1 = -\frac{1}{2} = -0.5$ (minimum)

Autokorelace prvního řádu MA(1) procesu nikdy nemůže překročit interval $[-0.5, 0.5]$.

---

### 3.4 Invertibilita procesu MA

Všimněme si, že hodnota autokorelace $\rho_1$ je symetrická vůči záměně $\theta \leftrightarrow \frac{1}{\theta}$:
$$\rho_1\left(\frac{1}{\theta}\right) = \frac{1/\theta}{1 + (1/\theta)^2} = \frac{1/\theta}{\frac{\theta^2 + 1}{\theta^2}} = \frac{\theta}{1 + \theta^2} = \rho_1(\theta)$$
Dva různé procesy — jeden s parametrem $\theta$ a druhý s parametrem $\frac{1}{\theta}$ — generují identickou autokorelační strukturu i rozptyl (při upraveném $\sigma^2$).

Abychom zajistili **jednoznačnou identifikovatelnost**, vyžadujeme podmínku **invertibility**.

#### Definice invertibility:
Proces MA($q$) je **invertibilní**, pokud jej lze přepsat jako stacionární proces AR($\infty$):
$$\Theta(L)^{-1} y_t = \varepsilon_t \implies y_t - \sum_{j=1}^{\infty} \pi_j y_{t-j} = \varepsilon_t$$
To nastává právě tehdy, když všechny kořeny charakteristického polynomu:
$$\Theta(z) = 1 + \theta_1 z + \theta_2 z^2 + \dots + \theta_q z^q = 0$$
leží **vně jednotkového kruhu**:
$$|z_i| > 1, \quad \forall i = 1, \dots, q$$

* Pro MA(1): $1 + \theta z = 0 \implies z = -\frac{1}{\theta}$.
  Podmínka $|z| > 1 \implies |-\frac{1}{\theta}| > 1 \iff |\theta| < 1$.

#### Důkaz převodu invertibilního MA(1) na AR($\infty$):
Mějme $y_t = (1 + \theta L)\varepsilon_t$. Pokud $|\theta| < 1$:
$$(1 + \theta L)^{-1} = \sum_{j=0}^{\infty} (-\theta)^j L^j = 1 - \theta L + \theta^2 L^2 - \theta^3 L^3 + \dots$$
Aplikací na $y_t$:
$$\left(\sum_{j=0}^{\infty} (-\theta)^j L^j\right) y_t = \varepsilon_t$$
$$y_t - \theta y_{t-1} + \theta^2 y_{t-2} - \theta^3 y_{t-3} + \dots = \varepsilon_t$$
$$y_t = \theta y_{t-1} - \theta^2 y_{t-2} + \theta^3 y_{t-3} - \dots + \varepsilon_t$$
Z tohoto vyjádření je zřejmé, že PACF procesu MA(1) nezaniká, nýbrž exponenciálně/alternovaně **doznívá k nule**.

---

## 4. Smíšené procesy — ARMA($p, q$)

### 4.1 Definice a Woldova reprezentace
Kombinací AR($p$) a MA($q$) vzniká proces **ARMA($p, q$)**:
$$y_t = c + \phi_1 y_{t-1} + \dots + \phi_p y_{t-p} + \varepsilon_t + \theta_1 \varepsilon_{t-1} + \dots + \theta_q \varepsilon_{t-q}$$
Zkráceně:
$$\Phi(L) y_t = c + \Theta(L) \varepsilon_t$$

Podle **Woldovy věty o dekompozici** lze každý kovariančně stacionární proces vyjádřit jako součet deterministické složky a nekonečného MA procesu:
$$y_t = \mu + \Psi(L)\varepsilon_t = \mu + \sum_{j=0}^{\infty} \psi_j \varepsilon_{t-j}$$
kde $\Psi(L) = \frac{\Theta(L)}{\Phi(L)}$ a $\sum_{j=0}^{\infty} \psi_j^2 < \infty$.

### 4.2 Past: Nadbytečnost parametrů (Common Roots)
Předpokládejme proces, který vypadá jako ARMA(1, 1):
$$(1 - 0.5L) y_t = (1 - 0.5L) \varepsilon_t$$
Pokud polynomem $(1 - 0.5L)$ formálně vydělíme obě strany:
$$y_t = \varepsilon_t$$
Zdánlivý ARMA(1, 1) model je ve skutečnosti pouhý bílý šum.

> [!danger] Pravidlo nesoudělnosti (Coprimality)
> Pro korektní identifikaci ARMA($p, q$) modelu nesmí polynomy $\Phi(z)$ a $\Theta(z)$ mít žádné **společné kořeny**. Pokud mají společný kořen, řád modelu $p$ i $q$ lze snížit o jedna bez změny generovaného stochastického procesu.

---

## 5. Nestacionarita, integrované procesy a ARIMA($p, d, q$)

Většina ekonomických časových řad (HDP, cenové indexy, mzdy) není stacionární v úrovních. Rozlišujeme dva fundamentálně odlišné typy nestacionarity:

### 5.1 Deterministický vs. Stochastický trend

```
Deterministický trend (Trend-Stationary):   Stochastický trend (Difference-Stationary):
y_t = alpha + beta*t + u_t                  y_t = y_{t-1} + beta + epsilon_t
(šoky u_t jsou dočasné)                     (šoky epsilon_t jsou permanentní)

   y_t ^                                       y_t ^
       |       /                                   |         /\
       |      /  /\                                |   /\   /  \
       |  /\ /  /  \                               |  /  \ /    \
       | /  V    \                                 | /    V
       |/_________\___> t                          |/____________> t
```

| Vlastnost | Deterministický trend (Trend-Stationary) | Stochastický trend (Difference-Stationary) |
| :--- | :--- | :--- |
| **Model** | $y_t = \alpha + \beta t + u_t, \quad u_t \sim \text{stacionární}$ | $y_t = \beta + y_{t-1} + \varepsilon_t$ |
| **Dopad šoku** | **Přechodný (transitory)**; řada se vrací k deterministické linii | **Trvalý (permanent)**; šok navždy změní úroveň řady |
| **Rozptyl v čase** | Konstantní: $Var(y_t) = Var(u_t) = \sigma_u^2$ | Roste lineárně s časem: $Var(y_t) = t \sigma^2 \to \infty$ |
| **Odstranění nestacionarity** | Odhad lineární regrese na čas $t$ a vzít rezidua | **Diferencování**: $\Delta y_t = y_t - y_{t-1}$ |
| **Chyba v postupu** | Pokud jej diferencujeme $\to$ vznikne neinvertibilní MA(1) | Pokud odhadneme regresi na čas $\to$ **falešná regrese** |

### 5.2 Integrovaný proces $I(d)$
Proces $\{y_t\}$ je **integrovaný řádu $d$**, značeno $y_t \sim I(d)$, pokud se po $d$-násobném diferencování stane stacionárním procesem $I(0)$, přičemž po $(d-1)$ diferencováních stacionární není:
$$\Delta^d y_t = (1 - L)^d y_t \sim I(0)$$

### 5.3 Model ARIMA($p, d, q$)
Kombinací $d$-násobné diference a procesu ARMA($p, q$) dostáváme **autoregresní integrovaný proces s klouzavými průměry**:
$$\Phi(L)(1 - L)^d y_t = c + \Theta(L)\varepsilon_t$$
kde $\Phi(z) = 0$ má všechny kořeny vně jednotkového kruhu a $\Theta(z) = 0$ splňuje podmínku invertibility.

---

### 5.4 Testování jednotkového kořene: Dickey-Fuller a ADF test

Uvažujme model AR(1):
$$y_t = \rho y_{t-1} + \varepsilon_t$$
Chceme testovat hypotézu stacionarity proti jednotkovému kořenu:
$$H_0: \rho = 1 \quad \text{vs.} \quad H_1: |\rho| < 1$$

Odečteme $y_{t-1}$ od obou stran rovnice:
$$y_t - y_{t-1} = \rho y_{t-1} - y_{t-1} + \varepsilon_t$$
$$\Delta y_t = (\rho - 1) y_{t-1} + \varepsilon_t$$
Označme $\gamma = \rho - 1$:
$$\Delta y_t = \gamma y_{t-1} + \varepsilon_t$$
Testované hypotézy se převedou na:
$$H_0: \gamma = 0 \quad (\text{jednotkový kořen, nestacionární})$$
$$H_1: \gamma < 0 \quad (\text{stacionární})$$

#### Problém nestandardního rozdělení
Za platnosti nulové hypotézy $\gamma = 0$ má odhad $\hat{\gamma}$ metodou OLS nestandardní rozdělení (vyjádřené funkcionály Wienerova procesu, nikoliv Studentovo t-rozdělení). Testová statistika:
$$\tau = \frac{\hat{\gamma}}{SE(\hat{\gamma})}$$
má **Dickey-Fullerovo rozdělení**, jehož kritické hodnoty jsou výrazně posunuty doleva (např. pro 5% hladinu významnosti bez konstanty je kritická hodnota přibližně $-1.95$, s konstantou $-2.86$, s konstantou a lineárním trendem $-3.41$).

#### Rozšířený Dickey-Fullerův test (ADF)
Pokud rezidua $\varepsilon_t$ vykazují autokorelaci, předpoklad testu selhává. ADF test řeší autokorelaci přidáním zpožděných diferencí vysvětlované proměnné:
$$\Delta y_t = c + \beta t + \gamma y_{t-1} + \sum_{i=1}^{k} \delta_i \Delta y_{t-i} + \varepsilon_t$$
Počet zpoždění $k$ se volí tak, aby byl šok $\varepsilon_t$ sériově nekorelovaný (obvykle pomocí informačních kritérií AIC/BIC).

---

## 6. Box-Jenkinsova metodologie v praxi

Metodologie pro modelování univariátních časových řad probíhá v cyklu tří kroků:

```
[ IDENTIFIKACE ] ---> [ ESTIMACE ] ---> [ DIAGNOSTICKÉ OVĚŘENÍ ]
       ^                                            |
       |--------------- (Model nevyhověl) <---------|
```

### Krok 1: Identifikace
1. Transformace dat k dosažení stacionarity (logaritmování pro stabilizaci rozptylu, diferencování k odstranění jednotkového kořene).
2. Určení řádů $p$ a $q$ z výběrových funkcí ACF a PACF:

| Teoretický model | Výběrová ACF | Výběrová PACF |
| :--- | :--- | :--- |
| **Bílý šum** | Všechna zpoždění nulová (uvnitř pásu $\pm \frac{1.96}{\sqrt{T}}$) | Všechna zpoždění nulová |
| **AR($p$)** | Doznívá exponenciálně / sinusově | **Usekne se** po zpoždění $p$ |
| **MA($q$)** | **Usekne se** po zpoždění $q$ | Doznívá exponenciálně / sinusově |
| **ARMA($p, q$)** | Doznívá od zpoždění $q$ | Doznívá od zpoždění $p$ |

### Krok 2: Estimace (Odhad parametrů)
* Pro AR modely: Metoda nejmenších čtverců (OLS) nebo Yule-Walker.
* Pro modely obsahující MA složku: Nelineární optimalizace metodou maximální věrohodnosti (Maximum Likelihood Estimation - MLE), podmíněná nebo přesná (přes Kalmanův filtr).

### Krok 3: Diagnostické ověření
Rezidua $\hat{\varepsilon}_t$ odhadnutého modelu musí splňovat vlastnosti bílého šumu.
* **Ljung-Boxův test:**
  $$Q_{LB} = T(T+2) \sum_{k=1}^{m} \frac{\hat{\rho}_k^2}{T - k} \sim \chi^2(m - p - q)$$
  * $H_0$: Rezidua jsou vzájemně nekorelovaná až do řádu $m$.
  * Chceme **nezamítnout** $H_0$ ($p\text{-hodnota} > 0.05$).
* **Volba modelu mezi kandidáty:** Pomocí informačních kritérií penalizujících počet parametrů:
  $$AIC = -2 \ln(\hat{L}) + 2(p + q + 1)$$
  $$BIC = -2 \ln(\hat{L}) + \ln(T)(p + q + 1)$$
  Vybíráme model s **nejnižší** hodnotou kritéria.

---

## 7. Běžné chyby, pasti a metodická úskalí

1. **Záměna stacionarity a invertibility:**
   * Stacionarita je vlastnost **AR** složky (schopnost vygenerovat konečný rozptyl).
   * Invertibilita je vlastnost **MA** složky (schopnost rekonstruovat nepozorované šoky z pozorovaných dat).
2. **Přeurčení modelu (Overfitting):**
   * Zařazení příliš mnoha zpoždění $p$ i $q$ vede ke kořenům, které se vzájemně vyruší (např. kořen $0.98$ v AR a $0.97$ v MA), a k obrovským standardním chybám parametrů.
3. **Přediferencování (Overdifferencing):**
   * Pokud aplikujeme diferenci na řadu, která už byla stacionární ($I(0)$), vytvoříme umělý jednotkový kořen v MA složce:
     $$y_t = \varepsilon_t \implies \Delta y_t = \varepsilon_t - \varepsilon_{t-1}$$
     Vznikne proces MA(1) s $\theta = -1$, který leží na hranici invertibility a zkresluje odhady.
4. **Ignorování deterministických složek v ADF testu:**
   * Pokud má řada zjevný lineární růstový trend a my v ADF testu vynecháme trend ($t$), test bude mít nulovou sílu a téměř nikdy nezamítne nulovou hypotézu jednotkového kořene.

---
---

# ČÁST II: Komplexní sbírka úloh s podrobným řešením

---

## Úroveň A: Mechanické početní úlohy

### Úloha A.1: Algebra operátoru zpoždění
Vyjádřete následující výrazy bez použití operátoru zpoždění $L$ a zjednodušte:
1. $A(L) y_t = (1 - 0.4L)(1 + 0.5L) y_t$
2. $B(L) y_t = (1 - L)^2 (1 - 0.2L) y_t$

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### Řešení části 1:
Roznásobíme polynomy podle běžných pravidel algebry:
$$(1 - 0.4L)(1 + 0.5L) = 1 + 0.5L - 0.4L - 0.2L^2 = 1 + 0.1L - 0.2L^2$$
Aplikujeme tento polynom na $y_t$:
$$(1 + 0.1L - 0.2L^2) y_t = y_t + 0.1 L y_t - 0.2 L^2 y_t = y_t + 0.1 y_{t-1} - 0.2 y_{t-2}$$

#### Řešení části 2:
Nejprve umocníme diferenční člen:
$$(1 - L)^2 = 1 - 2L + L^2$$
Nyní roznásobíme s třetím členem:
$$(1 - 2L + L^2)(1 - 0.2L) = 1(1 - 0.2L) - 2L(1 - 0.2L) + L^2(1 - 0.2L)$$
$$= 1 - 0.2L - 2L + 0.4L^2 + L^2 - 0.2L^3$$
$$= 1 - 2.2L + 1.4L^2 - 0.2L^3$$
Aplikujeme na $y_t$:
$$B(L) y_t = y_t - 2.2 y_{t-1} + 1.4 y_{t-2} - 0.2 y_{t-3}$$
</details>

---

### Úloha A.2: Test stacionarity procesů AR(1) a AR(2)
Rozhodněte o stacionaritě následujících procesů:
1. $y_t = 3 + 1.25 y_{t-1} + \varepsilon_t$
2. $y_t = 0.7 y_{t-1} - 0.1 y_{t-2} + \varepsilon_t$
3. $y_t = 1.4 y_{t-1} - 0.85 y_{t-2} + \varepsilon_t$



#### Řešení procesu 1:
Jde o AR(1) s parametrem $\phi = 1.25$.
Podmínka stacionarity pro AR(1) je $|\phi| < 1$.
Zde $|\phi| = 1.25 > 1$.
**Závěr:** Proces **není stacionární** (je explozivní).

#### Řešení procesu 2:
Jde o AR(2) s parametry $\phi_1 = 0.7$, $\phi_2 = -0.1$.
Ověříme tři podmínky trojúhelníku stacionarity:
1. $\phi_1 + \phi_2 < 1 \implies 0.7 + (-0.1) = 0.6 < 1$ (Splněno)
2. $\phi_2 - \phi_1 < 1 \implies -0.1 - 0.7 = -0.8 < 1$ (Splněno)
3. $|\phi_2| < 1 \implies |-0.1| = 0.1 < 1$ (Splněno)

Všechny tři podmínky jsou splněny současně.
**Závěr:** Proces **je stacionární**.

#### Řešení procesu 3:
Jde o AR(2) s parametry $\phi_1 = 1.4$, $\phi_2 = -0.85$.
Ověříme podmínky trojúhelníku stacionarity:
1. $\phi_1 + \phi_2 < 1 \implies 1.4 + (-0.85) = 0.55 < 1$ (Splněno)
2. $\phi_2 - \phi_1 < 1 \implies -0.85 - 1.4 = -2.25 < 1$ (Splněno)
3. $|\phi_2| < 1 \implies |-0.85| = 0.85 < 1$ (Splněno)

Všechny tři podmínky jsou splněny.
**Závěr:** Proces **je stacionární**.
*(Poznámka k dynamice: Diskriminant $D = \phi_1^2 + 4\phi_2 = 1.4^2 + 4(-0.85) = 1.96 - 3.4 = -1.44 < 0$. Kořeny jsou komplexně sdružené, proces vykazuje pseudocyklické chování.)*
</details>

---

### Úloha A.3: Test invertibility MA procesů
Ověřte podmínku invertibility pro následující procesy:
1. $y_t = \varepsilon_t - 0.6 \varepsilon_{t-1}$
2. $y_t = \varepsilon_t + 1.5 \varepsilon_{t-1} - 0.56 \varepsilon_{t-2}$

$<details>$
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### Řešení procesu 1:
Jde o MA(1) s $\theta = -0.6$.
Podmínka invertibility pro MA(1) vyžaduje $|\theta| < 1$.
Zde $|-0.6| = 0.6 < 1$.
**Závěr:** Proces **je invertibilní**.

#### Řešení procesu 2:
Jde o MA(2) s polynomem $\Theta(z) = 1 + 1.5 z - 0.56 z^2 = 0$.
Hledáme kořeny kvadratické rovnice:
$$-0.56 z^2 + 1.5 z + 1 = 0 \iff 0.56 z^2 - 1.5 z - 1 = 0$$
Diskriminant:
$$D = (-1.5)^2 - 4(0.56)(-1) = 2.25 + 2.24 = 4.49$$
$$\sqrt{D} = \sqrt{4.49} \approx 2.11896$$
Kořeny:
$$z_1 = \frac{1.5 + 2.11896}{2 \cdot 0.56} = \frac{3.61896}{1.12} \approx 3.231$$
$$z_2 = \frac{1.5 - 2.11896}{2 \cdot 0.56} = \frac{-0.61896}{1.12} \approx -0.5526$$

Podmínka invertibility vyžaduje, aby **všechny** kořeny měly absolutní hodnotu striktně větší než 1 ($|z_i| > 1$).
Pro kořen $z_2$ však platí:
$$|z_2| = |-0.5526| = 0.5526 < 1$$
Kořen $z_2$ leží uvnitř jednotkového kruhu.
**Závěr:** Proces **není invertibilní**.
</details>

---

## Úroveň B: Odvozování teoretických vlastností a momentů

### Úloha B.1: Přesné odvození momentů pro proces AR(1)
Mějme proces:
$$y_t = 4 + 0.6 y_{t-1} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, 4)$$
1. Vypočtěte nepodmíněnou střední hodnotu $\mu = E[y_t]$.
2. Vypočtěte nepodmíněný rozptyl $\gamma_0 = Var(y_t)$.
3. Vypočtěte autokovariance $\gamma_1, \gamma_2, \gamma_3$.
4. Určete autokorelace $\rho_1, \rho_2, \rho_3$.

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

Parametry procesu: $c = 4$, $\phi = 0.6$, $\sigma^2 = 4$.
Protože $|\phi| = 0.6 < 1$, proces je kovariančně stacionární.

#### 1. Nepodmíněná střední hodnota:
Ze stacionarity plyne $E[y_t] = E[y_{t-1}] = \mu$:
$$\mu = c + \phi \mu + E[\varepsilon_t]$$
$$\mu = 4 + 0.6 \mu + 0$$
$$\mu(1 - 0.6) = 4 \implies 0.4 \mu = 4 \implies \mu = \frac{4}{0.4} = 10$$

#### 2. Nepodmíněný rozptyl ($\gamma_0$):
Použijeme vzorec odvozený v sekci 2.3:
$$\gamma_0 = \frac{\sigma^2}{1 - \phi^2}$$
Dosadíme hodnoty:
$$\gamma_0 = \frac{4}{1 - 0.6^2} = \frac{4}{1 - 0.36} = \frac{4}{0.64} = 6.25$$

#### 3. Autokovariance ($\gamma_k$):
Využijeme vztah $\gamma_k = \phi^k \gamma_0$:
* $\gamma_1 = 0.6 \cdot \gamma_0 = 0.6 \cdot 6.25 = 3.75$
* $\gamma_2 = 0.6^2 \cdot \gamma_0 = 0.36 \cdot 6.25 = 2.25$
* $\gamma_3 = 0.6^3 \cdot \gamma_0 = 0.216 \cdot 6.25 = 1.35$

#### 4. Autokorelace ($\rho_k$):
Z definice $\rho_k = \phi^k$:
* $\rho_1 = 0.6^1 = 0.6$
* $\rho_2 = 0.6^2 = 0.36$
* $\rho_3 = 0.6^3 = 0.216$
*(Ověření: $\rho_1 = \frac{\gamma_1}{\gamma_0} = \frac{3.75}{6.25} = 0.6$ — sedí.)*
</details>

---

### Úloha B.2: Výpočet momentů procesu MA(2)
Mějme proces:
$$y_t = 10 + \varepsilon_t - 0.8 \varepsilon_{t-1} + 0.3 \varepsilon_{t-2}, \quad \sigma^2 = 2$$
1. Určete střední hodnotu $\mu$.
2. Vypočtěte rozptyl $\gamma_0$.
3. Vypočtěte autokovariance $\gamma_1, \gamma_2, \gamma_3$.
4. Určete hodnoty autokorelační funkce $\rho_1, \rho_2, \rho_3$.

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

Parametry: $\mu = 10$, $\theta_1 = -0.8$, $\theta_2 = 0.3$, $\sigma^2 = 2$.

#### 1. Střední hodnota:
$$E[y_t] = 10 + E[\varepsilon_t] - 0.8 E[\varepsilon_{t-1}] + 0.3 E[\varepsilon_{t-2}] = 10 + 0 + 0 + 0 = 10$$

#### 2. Rozptyl ($\gamma_0$):
$$\gamma_0 = \sigma^2 (1 + \theta_1^2 + \theta_2^2)$$
$$\gamma_0 = 2 \cdot (1 + (-0.8)^2 + 0.3^2) = 2 \cdot (1 + 0.64 + 0.09) = 2 \cdot 1.73 = 3.46$$

#### 3. Autokovariance:
Využijeme vzorec $\gamma_k = \sigma^2 \sum_{j=0}^{q-k} \theta_j \theta_{j+k}$, kde $\theta_0 = 1$:

* **Pro $k = 1$:**
  $$\gamma_1 = \sigma^2 (\theta_0 \theta_1 + \theta_1 \theta_2) = 2 \cdot (1 \cdot (-0.8) + (-0.8) \cdot 0.3)$$
  $$\gamma_1 = 2 \cdot (-0.8 - 0.24) = 2 \cdot (-1.04) = -2.08$$

* **Pro $k = 2$:**
  $$\gamma_2 = \sigma^2 (\theta_0 \theta_2) = 2 \cdot (1 \cdot 0.3) = 0.6$$

* **Pro $k = 3$ ($k > q$):**
  Jelikož pro MA(2) je $q = 2$, pro všechna $k > 2$ jsou šoky navzájem nezávislé:
  $$\gamma_3 = 0$$

#### 4. Autokorelace ($\rho_k = \frac{\gamma_k}{\gamma_0}$):
* $\rho_1 = \frac{-2.08}{3.46} \approx -0.60116$
* $\rho_2 = \frac{0.6}{3.46} \approx 0.17341$
* $\rho_3 = 0$
</details>

---

### Úloha B.3: Rekonstrukce parametrů AR(2) z Yule-Walkerových rovnic
Předpokládejme, že u centrovaného stacionárního procesu AR(2):
$$y_t = \phi_1 y_{t-1} + \phi_2 y_{t-2} + \varepsilon_t$$
byly zjištěny první dvě hodnoty autokorelační funkce: $\rho_1 = 0.7$ a $\rho_2 = 0.5$.
1. Odvoďte a spočítejte parametry $\phi_1$ a $\phi_2$.
2. Vypočtěte hodnotu autokorelace třetího řádu $\rho_3$.
3. Ověřte, zda odhadnutý model splňuje podmínky stacionarity.

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### 1. Výpočet parametrů $\phi_1, \phi_2$:
Ze sekce 2.4 známe Yule-Walkerovy rovnice pro $k = 1$ a $k = 2$:
1. $\rho_1 = \phi_1 + \phi_2 \rho_1$
2. $\rho_2 = \phi_1 \rho_1 + \phi_2$

Dosadíme známé hodnoty $\rho_1 = 0.7$ a $\rho_2 = 0.5$:
$$\begin{cases} 0.7 = \phi_1 + 0.7 \phi_2 \\ 0.5 = 0.7 \phi_1 + \phi_2 \end{cases}$$

Z první rovnice vyjádříme $\phi_1$:
$$\phi_1 = 0.7 - 0.7 \phi_2 = 0.7(1 - \phi_2)$$

Dosadíme do druhé rovnice:
$$0.5 = 0.7 \cdot [0.7(1 - \phi_2)] + \phi_2$$
$$0.5 = 0.49(1 - \phi_2) + \phi_2$$
$$0.5 = 0.49 - 0.49 \phi_2 + \phi_2$$
$$0.5 - 0.49 = 0.51 \phi_2$$
$$0.01 = 0.51 \phi_2 \implies \phi_2 = \frac{0.01}{0.51} = \frac{1}{51} \approx 0.019608$$

Zpětně dopočítáme $\phi_1$:
$$\phi_1 = 0.7 \cdot \left(1 - \frac{1}{51}\right) = 0.7 \cdot \frac{50}{51} = \frac{35}{51} \approx 0.686275$$

#### 2. Výpočet $\rho_3$:
Pro $k = 3$ má Yule-Walkerova rovnice tvar:
$$\rho_3 = \phi_1 \rho_2 + \phi_2 \rho_1$$
Dosadíme vypočtené hodnoty:
$$\rho_3 = \left(\frac{35}{51}\right) \cdot 0.5 + \left(\frac{1}{51}\right) \cdot 0.7 = \frac{17.5 + 0.7}{51} = \frac{18.2}{51} \approx 0.35686$$

#### 3. Ověření stacionarity:
Otestujeme podmínky trojúhelníku stacionarity pro $\phi_1 \approx 0.6863$ a $\phi_2 \approx 0.0196$:
1. $\phi_1 + \phi_2 = 0.6863 + 0.0196 = 0.7059 < 1$ (Platí)
2. $\phi_2 - \phi_1 = 0.0196 - 0.6863 = -0.6667 < 1$ (Platí)
3. $|\phi_2| = |0.0196| = 0.0196 < 1$ (Platí)

Model je **stacionární**.
</details>

---

## Úroveň C: Aplikační a kontextové slovní úlohy

### Úloha C.1: Identifikace modelu z výběrové ACF a PACF
Analytik centrální banky zkoumá stacionární časovou řadu mezikvartální inflace (vzorek $T = 400$ pozorování). Standardní chyba pro testování nulovosti korelačních koeficientů je $\frac{1.96}{\sqrt{400}} = \frac{1.96}{20} = 0.098$. V tabulce jsou uvedeny odhady výběrové ACF ($\hat{\rho}_k$) a PACF ($\hat{\phi}_{kk}$):

| Lag ($k$) | 1 | 2 | 3 | 4 | 5 | 6 |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Výběrová ACF ($\hat{\rho}_k$)** | **0.58** | **0.34** | **0.21** | 0.11 | 0.07 | 0.03 |
| **Výběrová PACF ($\hat{\phi}_{kk}$)** | **0.58** | -0.02 | 0.04 | -0.05 | 0.01 | -0.02 |

*(Tučně zvýrazněné hodnoty jsou statisticky významně odlišné od nuly na 5% hladině významnosti, tj. v absolutní hodnotě $> 0.098$.)*

1. Který model (včetně řádu $p$ a $q$) nejlépe odpovídá těmto datům? Svůj závěr exaktně zdůvodněte.
2. Zapište rovnici identifikovaného modelu s odhadnutými parametry (využijte vztahy mezi výběrovými koeficienty a parametry).

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### 1. Identifikace řádu:
* **Analýza PACF:** Výběrová parciální autokorelace je vysoce statisticky významná na lagu $1$ ($\hat{\phi}_{11} = 0.58 > 0.098$), ale na všech vyšších zpožděních ($k = 2, 3, 4, 5, 6$) jsou hodnoty zanedbatelné a leží hluboko uvnitř intervalu spolehlivosti $[-0.098; 0.098]$. PACF se tedy **ostře usekne za lagem 1**.
* **Analýza ACF:** Hodnoty autokorelační funkce plynule exponenciálně klesají ($0.58 \to 0.34 \to 0.21 \to 0.11 \to \dots$). Zde platí: $0.58^2 = 0.3364 \approx 0.34$ a $0.58^3 = 0.195 \approx 0.21$.

Tato kombinace vlastností (useknutí PACF po řádu $p=1$ a exponenciální doznívání ACF s faktorem $\hat{\rho}_1^k$) je teoretickou signaturou **procesu AR(1)**.

#### 2. Sestavení rovnice:
Pro proces AR(1) platí teoretický vztah $\phi_1 = \rho_1 = \phi_{11}$.
Bodový odhad autoregresního parametru je tedy:
$$\hat{\phi}_1 = 0.58$$
Rovnice identifikovaného modelu:
$$y_t = c + 0.58 y_{t-1} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2)$$
kde $c = \mu(1 - 0.58) = 0.42\mu$.
</details>

---

### Úloha C.2: Důsledky falešného odstranění trendu (Spurious Detrending)
Předpokládejme, že skutečný proces tvorby dat je ryzí náhodná procházka (stochastický trend):
$$y_t = y_{t-1} + \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2), \quad y_0 = 0$$
Nezkušený ekonometrik se domnívá, že řada má pouze deterministický trend, a aplikuje na ni lineární regresi:
$$y_t = \alpha + \beta t + u_t$$
a následně pracuje s reziduy $\hat{u}_t$ v domnění, že získal stacionární řadu.
1. Ukažte, proč je rozptyl reziduí $\hat{u}_t$ nestacionární.
2. Jaký je teoretický rozdíl v dopadu makroekonomického šoku $\varepsilon_t$, pokud proces nesprávně považujeme za trendově stacionární oproti diferenčně stacionárnímu?

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### 1. Analýza rozptylu:
Skutečný proces náhodné procházky lze rozepsat jako:
$$y_t = \sum_{i=1}^t \varepsilon_i$$
Její rozptyl je:
$$Var(y_t) = Var\left(\sum_{i=1}^t \varepsilon_i\right) = \sum_{i=1}^t Var(\varepsilon_i) = t \sigma^2$$
I když od $y_t$ odečteme odhadnutý deterministický lineární trend $\hat{\alpha} + \hat{\beta} t$, reziduum zůstává lineární kombinací původních proměnných:
$$\hat{u}_t \approx y_t - (\alpha + \beta t)$$
Protože deterministický člen $\alpha + \beta t$ nemá žádný rozptyl ($Var(\alpha + \beta t) = 0$), platí:
$$Var(\hat{u}_t) \approx Var(y_t) = t \sigma^2 \xrightarrow{t \to \infty} \infty$$
Odstranění lineárního trendu neodstranilo akumulaci šoků. Reziduum $\hat{u}_t$ má časově závislý rozptyl, a proto **není stacionární**.

Navíc autokorelace reziduí $\hat{u}_t$ konverguje pro velké vzorky k deterministické funkci zpoždění a nikdy nezaniká k nule.

#### 2. Makroekonomická interpretace dopadu šoku:
* **Pokud je model správně chápán jako diferenčně stacionární ($I(1)$):**
  $$\frac{\partial y_{t+k}}{\partial \varepsilon_t} = 1 \quad \forall k \ge 0$$
  Jednorázový negativní šok (např. finanční krize) sníží hladinu HDP **trvale**. HDP se nikdy nevrátí na původní trajektorii; šok se stává trvalou součástí základny.
* **Pokud je model chybně považován za trendově stacionární:**
  Ekonometrik předpokládá model $y_t = \alpha + \beta t + u_t$, kde $u_t$ považuje za stacionární proces s návratem ke střední hodnotě ($u_t \to 0$). Z toho by chybně vyvodil, že:
  $$\lim_{k \to \infty} \frac{\partial y_{t+k}}{\partial \varepsilon_t} = 0$$
  Hospodářská politika by na základě tohoto chybného modelu očekávala, že ekonomika se sama přirozeně vrátí na původní předkrizový trend bez nutnosti strukturních adaptací, což vede k fatálním chybám v prognózách.
</details>

---

## Úroveň D: Výzvy a kombinované teoretické problémy

### Úloha D.1: Nekonečná reprezentace a rozptyl předpovědi
Mějme stacionární proces ARMA(1, 1):
$$(1 - 0.8L) y_t = (1 - 0.5L) \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2)$$

1. Najděte koeficienty $\psi_j$ v kauzální reprezentaci procesu:
   $$y_t = \sum_{j=0}^{\infty} \psi_j \varepsilon_{t-j}$$
   Vypočtěte explicitně hodnoty pro $\psi_0, \psi_1, \psi_2, \psi_3$.
2. Určete optimální bodovou předpověď $\hat{y}_{t+h|t} = E[y_{t+h} \mid \mathcal{I}_t]$ pro horizonty $h = 1$ a $h = 2$, kde $\mathcal{I}_t = \{y_t, y_{t-1}, \dots, \varepsilon_t, \varepsilon_{t-1}, \dots\}$.
3. Odvoďte chybu předpovědi $e_{t+h|t} = y_{t+h} - \hat{y}_{t+h|t}$ a její rozptyl $Var(e_{t+h|t})$ pro obecné $h \ge 1$. Co se děje s rozptylem chyby předpovědi, když $h \to \infty$?

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### 1. Odvození koeficientů $\psi_j$:
Z definice kauzální reprezentace:
$$\Psi(L) = \frac{\Theta(L)}{\Phi(L)} \implies \Phi(L) \Psi(L) = \Theta(L)$$
Dosadíme známé polynomy:
$$(1 - 0.8L) \left(\psi_0 + \psi_1 L + \psi_2 L^2 + \psi_3 L^3 + \dots\right) = 1 - 0.5L$$
Roznásobíme levou stranu:
$$\psi_0 + \psi_1 L + \psi_2 L^2 + \psi_3 L^3 + \dots - 0.8\psi_0 L - 0.8\psi_1 L^2 - 0.8\psi_2 L^3 - \dots = 1 - 0.5L$$
$$\psi_0 + (\psi_1 - 0.8\psi_0) L + (\psi_2 - 0.8\psi_1) L^2 + (\psi_3 - 0.8\psi_2) L^3 + \dots = 1 - 0.5L$$

Porovnáním koeficientů u jednotlivých mocnin $L$:
* **$L^0$:** $\psi_0 = 1$
* **$L^1$:** $\psi_1 - 0.8\psi_0 = -0.5 \implies \psi_1 = 0.8(1) - 0.5 = 0.3$
* **$L^2$:** $\psi_2 - 0.8\psi_1 = 0 \implies \psi_2 = 0.8\psi_1 = 0.8(0.3) = 0.24$
* **$L^3$:** $\psi_3 - 0.8\psi_2 = 0 \implies \psi_3 = 0.8\psi_2 = 0.8(0.24) = 0.192$

Obecný vzorec pro $j \ge 1$:
$$\psi_j = 0.3 \cdot (0.8)^{j-1}$$

#### 2. Bodová předpověď:
Rovnice modelu v čase $t+h$:
$$y_{t+h} = 0.8 y_{t+h-1} + \varepsilon_{t+h} - 0.5 \varepsilon_{t+h-1}$$

* **Pro horizont $h = 1$:**
  $$y_{t+1} = 0.8 y_t + \varepsilon_{t+1} - 0.5 \varepsilon_t$$
  Aplikujeme podmíněnou střední hodnotu $E[\cdot \mid \mathcal{I}_t]$:
  $$\hat{y}_{t+1|t} = 0.8 y_t + E[\varepsilon_{t+1} \mid \mathcal{I}_t] - 0.5 \varepsilon_t$$
  Jelikož šok $\varepsilon_{t+1}$ je budoucí a nezávislý na informacích do času $t$, platí $E[\varepsilon_{t+1} \mid \mathcal{I}_t] = 0$:
  $$\hat{y}_{t+1|t} = 0.8 y_t - 0.5 \varepsilon_t$$

* **Pro horizont $h = 2$:**
  $$y_{t+2} = 0.8 y_{t+1} + \varepsilon_{t+2} - 0.5 \varepsilon_{t+1}$$
  Aplikujeme podmíněnou střední hodnotu:
  $$\hat{y}_{t+2|t} = 0.8 \hat{y}_{t+1|t} + 0 - 0 = 0.8(0.8 y_t - 0.5 \varepsilon_t) = 0.64 y_t - 0.4 \varepsilon_t$$

#### 3. Chyba předpovědi a její rozptyl:
Vyjádříme $y_{t+h}$ pomocí kauzální reprezentace:
$$y_{t+h} = \sum_{j=0}^{\infty} \psi_j \varepsilon_{t+h-j} = \sum_{j=0}^{h-1} \psi_j \varepsilon_{t+h-j} + \sum_{j=h}^{\infty} \psi_j \varepsilon_{t+h-j}$$
Podmíněná střední hodnota:
$$\hat{y}_{t+h|t} = E[y_{t+h} \mid \mathcal{I}_t] = 0 + \sum_{j=h}^{\infty} \psi_j \varepsilon_{t+h-j}$$
Chyba předpovědi:
$$e_{t+h|t} = y_{t+h} - \hat{y}_{t+h|t} = \sum_{j=0}^{h-1} \psi_j \varepsilon_{t+h-j} = \varepsilon_{t+h} + \psi_1 \varepsilon_{t+h-1} + \dots + \psi_{h-1} \varepsilon_{t+1}$$
Rozptyl chyby předpovědi (díky nekorelovanosti šoků):
$$Var(e_{t+h|t}) = Var\left( \sum_{j=0}^{h-1} \psi_j \varepsilon_{t+h-j} \right) = \sigma^2 \sum_{j=0}^{h-1} \psi_j^2$$

Dosadíme explicitní koeficienty $\psi_0 = 1$ a $\psi_j = 0.3(0.8)^{j-1}$ pro $j \ge 1$:
$$Var(e_{t+h|t}) = \sigma^2 \left[ 1 + \sum_{j=1}^{h-1} \left(0.3 \cdot 0.8^{j-1}\right)^2 \right] = \sigma^2 \left[ 1 + 0.09 \sum_{k=0}^{h-2} (0.64)^k \right]$$

Limitní chování pro $h \to \infty$:
$$\lim_{h \to \infty} Var(e_{t+h|t}) = \sigma^2 \left[ 1 + 0.09 \sum_{k=0}^{\infty} (0.64)^k \right] = \sigma^2 \left[ 1 + \frac{0.09}{1 - 0.64} \right] = \sigma^2 \left[ 1 + \frac{0.09}{0.36} \right] = \sigma^2 [1 + 0.25] = 1.25 \sigma^2$$
Rozptyl chyby předpovědi nestoupá do nekonečna (jako by tomu bylo u nestacionární řady), nýbrž konverguje k nepodmíněnému rozptylu stacionárního procesu $\gamma_0 = Var(y_t) = 1.25\sigma^2$.
</details>

---

### Úloha D.2: Důkaz vzniku MA(1) při přediferencování (Overdifferencing)
Mějme stacionární proces bílého šumu:
$$y_t = \varepsilon_t, \quad \varepsilon_t \sim WN(0, \sigma^2)$$
Analytik se mylně domnívá, že řada vykazuje jednotkový kořen, a provede první diferenci:
$$w_t = \Delta y_t = y_t - y_{t-1}$$

1. Zapište proces $w_t$ v operátorovém tvaru a identifikujte jeho třídu a řád.
2. Vypočtěte autokovarianční funkci $\gamma_k^w$ a autokorelační funkci $\rho_k^w$ procesu $w_t$.
3. Dokažte, že proces $w_t$ je na samé hranici invertibility. Jaké fatální numerické a analytické důsledky to má pro odhad parametrů metodou maximální věrohodnosti?

<details>
<summary><b>Zobrazit detailní řešení s postupem</b></summary>

#### 1. Identifikace procesu:
Dosadíme $y_t = \varepsilon_t$ do diferenční rovnice:
$$w_t = \varepsilon_t - \varepsilon_{t-1} = (1 - L)\varepsilon_t$$
Proces $w_t$ je lineární kombinací současného a jednoho zpožděného šoku s parametrem $\theta = -1$.
Jedná se tedy o **proces MA(1)** bez konstanty:
$$w_t = \varepsilon_t + \theta \varepsilon_{t-1}, \quad \text{kde } \theta = -1$$

#### 2. Výpočet momentů:
* **Rozptyl:**
  $$\gamma_0^w = Var(\varepsilon_t - \varepsilon_{t-1}) = Var(\varepsilon_t) + (-1)^2 Var(\varepsilon_{t-1}) = \sigma^2 + \sigma^2 = 2\sigma^2$$
* **Autokovariance na lagu 1:**
  $$\gamma_1^w = E[(\varepsilon_t - \varepsilon_{t-1})(\varepsilon_{t-1} - \varepsilon_{t-2})] = -E[\varepsilon_{t-1}^2] = -\sigma^2$$
* **Autokovariance na lagu $k \ge 2$:**
  Jelikož v součinu $(\varepsilon_t - \varepsilon_{t-1})(\varepsilon_{t-k} - \varepsilon_{t-k-1})$ nevystupují pro $k \ge 2$ žádné společné indexy:
  $$\gamma_k^w = 0 \quad \forall k \ge 2$$
* **Autokorelace:**
  $$\rho_1^w = \frac{\gamma_1^w}{\gamma_0^w} = \frac{-\sigma^2}{2\sigma^2} = -0.5$$
  $$\rho_k^w = 0 \quad \forall k \ge 2$$

#### 3. Důkaz neinvertibility a důsledky:
Charakteristický polynom MA složky:
$$\Theta(z) = 1 + \theta z = 1 - z = 0 \implies z = 1$$
Kořen charakteristického polynomu leží přesně **na jednotkovém kruhu** ($|z| = 1$), nikoli striktně vně ($|z| > 1$). Proces $w_t$ je tedy **neinvertibilní**.

**Důsledky pro praxi a estimaci:**
1. **Nemožnost AR($\infty$) reprezentace:** Inverze $(1 - L)^{-1} = 1 + L + L^2 + \dots$ diverguje. Nelze vyjádřit šok $\varepsilon_t$ jako konvergentní sumu minulých pozorování:
   $$\varepsilon_t = \sum_{j=0}^{\infty} w_{t-j} = w_t + w_{t-1} + w_{t-2} + \dots$$
   Minulost má v tomto vyjádření neklesající váhu (váha je stále $1$).
2. **Kolaps odhadovacích algoritmů:** Při numerické optimalizaci metody maximální věrohodnosti (MLE) algoritmy hledají parametr $\hat{\theta} \in (-1, 1)$. Pokud skutečný parametr leží na hranici $\theta = -1$, numerické gradientní metody (Newton-Raphson, BFGS) selhávají, dochází k tzv. *pile-up problému* (parametr uvízne přesně na hranici $-1$), kovarianční matice odhadů degeneruje a standardní asymptotické testy (t-testy, wald testy) přestávají platit.
</details>

---

## Kontrolní matice pojmů a rychlé shrnutí

```
              Charakteristické vlastnosti v kostce:
+---------------+-----------------------+-----------------------+
| Model         | Teoretická ACF        | Teoretická PACF       |
+---------------+-----------------------+-----------------------+
| AR(p)         | Nekonečná             | Konečná               |
|               | (doznívá k nule)      | (usekne se po p)      |
+---------------+-----------------------+-----------------------+
| MA(q)         | Konečná               | Nekonečná             |
|               | (usekne se po q)      | (doznívá k nule)      |
+---------------+-----------------------+-----------------------+
| ARMA(p, q)    | Nekonečná             | Nekonečná             |
|               | (doznívá od q)        | (doznívá od p)        |
+---------------+-----------------------+-----------------------+
| ARIMA(p, d, q)| Nestacionární         | Nestacionární         |
|               | (extrémně pomalý pád) | (\phi_11 blízko 1.0)  |
+---------------+-----------------------+-----------------------+
```