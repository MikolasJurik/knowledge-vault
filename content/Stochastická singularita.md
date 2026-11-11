Shrnutí: 
Mám míň zdrojů šoků než je počet pozorovaných proměných → tyto proměné jsou proto deterministickou linearní kombinací těch šoků → kovariační matice pozorovaných proměných je singulární = neinvertibilní = nelze spočítat věrohodnostní funkci(pravděpodobnost pozorovaných dat je 0, log věrohodnost jde do - $\inf$) ani Kalmanův filtr


## 1. Intuitivní vysvětlení 

Představme si DSGE model se dvěma nezávislými strukturálními šoky:
- **technologický šok** ($\varepsilon_A$)
- **preferenční šok** ($\varepsilon_D$)

Model tedy obsahuje pouze **2 nezávislé zdroje náhodných výkyvů**.

Pokud se pokusíme model odhadnout na **3 pozorovaných proměnných**:
- HDP ($Y$)
- spotřeba ($C$)
- investice ($I$)

> narážíme na problém: všechny 3 veličiny jsou deterministickou lineární kombinací pouhých 2 šoků. ==🟣Model tím pádem implikuje **přesnou lineární závislost** mezi jejich inovacemi. Pokud data v realitě této přesné závislosti neodpovídají (což téměř nikdy neodpovídají), věrohodnostní funkce (*likelihood*) se zhroutí (pravděpodobnost pozorování dat je nulová, log-věrohodnost jde do $-\infty$).==

---

## 2. Matematická formulace

Mějme zjednodušený linearizovaný stavový zápis měření:

$$
\begin{pmatrix} Y_t \\ C_t \\ I_t \end{pmatrix} = \underbrace{\begin{pmatrix} h_{11} & h_{12} \\ h_{21} & h_{22} \\ h_{31} & h_{32} \end{pmatrix}}_{H_{(3 \times 2)}} \begin{pmatrix} \varepsilon_{A,t} \\ \varepsilon_{D,t} \end{pmatrix}
$$

Kovarianční matice pozorovaných veličin je dána vztahem:

$$
\Sigma = H \Omega H'
$$

kde $\Omega = \operatorname{Var}(\boldsymbol{\varepsilon}_t)$ je kovarianční matice strukturálních šoků o rozměru $2 \times 2$.

Z vlastností hodnosti maticového součinu plyne:

$$
\operatorname{rank}(\Sigma) \le \min\big(\operatorname{rank}(H), \operatorname{rank}(\Omega)\big) \le 2
$$

Matice $\Sigma$ má však rozměr $3 \times 3$, ale její hodnost je maximálně $2$.

> [!danger] Jádro problému
> Pokud platí:
> $$\text{Počet pozorovaných proměnných } (n_y) > \text{Počet nezávislých šoků } (n_\varepsilon)$$
> kovarianční matice $\Sigma$ je **singulární** (není invertibilní). Nelze spočítat Kalmanův filtr ani věrohodnostní funkci.

---

## 3. Způsoby řešení v DSGE modelech

### A) Snížení počtu pozorovaných proměnných
Nejjednodušší a metodicky nejčistší cesta:
* Máme-li $n_\varepsilon$ šoků, vybereme maximálně $n_y \le n_\varepsilon$ klíčových pozorovaných řad (např. pouze Y a C →výstup a spotřebu).

### B) Přidání chyb měření (*Measurement Errors*)
Do **měřicích rovnic** (*measurement equations*) doplníme dodatečné náhodné složky $\eta_t$:

$$
\begin{aligned}
Y_t^{obs} &= Y_t + \eta_{Y,t}, \quad &\eta_{Y,t} &\sim \mathcal{N}(0, \sigma_{\eta,Y}^2) \\
C_t^{obs} &= C_t + \eta_{C,t}, \quad &\eta_{C,t} &\sim \mathcal{N}(0, \sigma_{\eta,C}^2)
\end{aligned}
$$

Každá chyba měření funguje jako dodatečný nezávislý šok, čímž obnoví plnou hodnost matice $\Sigma$.

> [!warning] Upozornění
> Chyby měření by měly mít **ekonomické nebo statistické opodstatnění** (např. nepřesnost statistického vykazování investic), neměly by sloužit pouze jako mechanická záplata pro obejití singularity.

---

## 4. Porovnání: Singularita vs. Identifikovatelnost

Tyto dva koncepty se často pletou, ale řeší odlišné vlastnosti systému:

| Vlastnost    | **Stochastická singularita**                              | **Neidentifikovatelnost parametrů**                               |
| :----------- | :-------------------------------------------------------- | :---------------------------------------------------------------- |
| **Problém**  | $n_y > n_\varepsilon$ (málo šoků na počet datových řad)   | Různé hodnoty parametrů $\theta$ vedou ke stejné věrohodnosti     |
| **Příčina**  | Kovarianční matice dat nemá plnou hodnost → je singulární | Informační matice / Hessian věrohodnosti je singulární            |
| **Důsledek** | Nelze  vyhodnotit věrohodnostní funkci ($-\infty$)        | Parametry nelze z dat jednoznačně odhadnout (plochá posteriorita) |

## 5. Otázky


### 1: Zlaté pravidlo a stochastická singularita
Představ si, že tvůj model má v bloku `varexo` pouze **1 strukturální šok** ($\varepsilon_A$). Ty chceš model odhadnout na datech a do bloku `varobs` napíšeš:
```dynare
varobs y_obs c_obs;
```
*(bez jakýchkoliv chyb měření)*.

**Otázka:** Co se stane, když spustíš příkaz `estimation`? Jaký je matematický důvod selhání výpočtu?

> [!check]- Řešení a detailní vysvětlení
> **Tvoje odpověď:** *„Problém stochastické singularity – mám 1 zdroj šoku na 2 pozorované proměnné... rank kovarianční matice bude neplnohodnotný.“*
> 
> **Hodnocení:** **Výborně! Přesně v tom je podstata.**
> 
> **Dovysvětlení matematického mechanismu:**
> 1. **Proč k tomu dojde v Kalmanově filtru:**
>    - Kalmanův filtr v každém kroku počítá kovarianční matici předpovědních chyb pozorovaných veličin: 
>      $$F_t = \operatorname{Var}(y_t^{obs} - \hat{y}_t^{obs})$$
>    - Matice $F_t$ má rozměr $2 \times 2$ (máme 2 řady: $y$ a $c$). Protože ale obě proměnné pohání pouze **1 šok**, platí $\operatorname{rank}(F_t) = 1$.
>    - Pro výpočet věrohodnosti (*likelihood*) filtr potřebuje matici **invertovat** ($F_t^{-1}$) a spočítat její **determinant** ($\det F_t$):
>      $$\ln L \propto -\frac{1}{2} \sum \left( \ln |F_t| + v_t' F_t^{-1} v_t \right)$$
>    - Jelikož matice nemá plnou hodnost, platí $\det(F_t) = 0$ a $F_t^{-1}$ neexistuje. Věrohodnost spadne do $-\infty$ a optimalizátor selže.

---

### 2: Geometrická intuice (lineární závislost)
**Otázka:** Co přesně model z otázky 1 předpokládá o vztahu mezi $y_t$ a $c_t$, co v reálných datech neplatí a proč data neprojdou?

> [!check]- Řešení a detailní vysvětlení
> **Tvoje odpověď:** *„Předpokládá, že ten systém je  deterministický, nikoliv stochastický.“*
> 
> 
> **Správné vysvětlení:**
> - Model s jedním šokem totiž pro tyto dvě proměnné implikuje **přísně deterministický lineární vztah**.
> - V modelu platí např. $y_t = h_y \varepsilon_t$ a $c_t = h_c \varepsilon_t$. Z toho plyne přímá závislost:
>   $$c_t = \left(\frac{h_c}{h_y}\right) y_t$$
> - V geometrickém pohledu model říká: *„Všechna data v rovině $[y_t, c_t]$ musí ležet na jediné přímce.“*
> - V reálných datech ale existují preference, změny daní, chyby vykazování atd., takže body tvoří rozptýlený „mrak“. Jakmile se v datech objeví jediný bod mimo tuto modelovou přímku, model mu přiřadí **nulovou pravděpodobnost výskytu**.

---

### 3: Singularita vs. Identifikace parametrů
Kolega z týmu ti řekne: 
> *„Máme v modelu 3 šoky a odhadujeme na 2 pozorovaných veličinách. Takže máme vyhráno a určitě všechny parametry bez problému odhadneme.“*

**Otázka:** Má pravdu? Pokud ne, jaký problém může nastat a v čem se liší od stochastické singularity?
> [!check]- Řešení a detailní vysvětlení
> **Tvoje odpověď:** *„Parametry mohou být neidentifikovatelné – odlišné parametry mohou plodit velmi podobný efekt a model je tak nedokáže odhadnout.“*
> 
> 
> **Dovysvětlení:**
> - Splnění podmínky $n_\varepsilon \ge n_y$ (3 šoky $\ge$ 2 proměnné) řeší **pouze** stochastickou singularitu – znamená to jen tolik, že *lze spočítat věrohodnostní funkci*.
> - Nezaručuje to však **identifikovatelnost parametrů** (*parameter identification*):
>   1. **Strukturální neidentifikovatelnost:** Dva parametry vstupují do rovnic vždy jako součin (např. $\alpha \cdot \beta = 0{,}5$). Může to být $1 \times 0{,}5$ nebo $2 \times 0{,}25$. Data nemají šanci je od sebe rozlišit.
>   2. **Slabá identifikovatelnost (*weak identification*):** Změna parametru téměř nepohne s chováním modelu (plochá věrohodnostní funkce).
> - **Rozdíl:** Při singularitě věrohodnost vůbec nespočítáš. Při neidentifikovatelnosti ji spočítáš, ale má ploché dno/hřeben a posteriorní rozdělení jen zkopíruje apriorní rozdělení.

---

### 4: Chyby měření v Dynare
Rozhodl ses vyřešit singularitu přidáním chyby měření k investicím:
$$I_t^{obs} = I_t + \eta_{I,t}$$

**Otázka:** Kam přesně v Dynare zapíšeš $\eta_{I,t}$ (do jakých bloků)?

> [!check]- Řešení a detailní vysvětlení
> **Tvoje odpověď:** *„Do měřící rovnice.“*
> 
> **Hodnocení:** **Správně z ekonomického hlediska, v Dynare to lze udělat 2 způsoby:**
> 
> 1. **Explicitní zápis přes šok (nejčastější u začátečníků):**
>    - Přidáš $\eta_I$ do bloku `varexo eta_I;`
>    - V bloku `model;` vytvoříš měřicí rovnici: `I_obs = I + eta_I;`
>    - V bloku `estimated_params;` odhaduješ její směrodatnou odchylku: `stderr eta_I, ...;`
> 
> 2. **Nativní zápis v Dynare (zkrácený a čistší):**
>    - Nemusíš upravovat modelové rovnice. V bloku `estimated_params` přímo deklaruješ chybu měření pro pozorovanou proměnnou:
>    ```dynare
>    estimated_params;
>        stderr I_obs, inv_gamma_pdf, 0.01, inf;
>    end;
>    ```
>    Dynare sám na pozadí přičte bílý šum k dané rovnici v Kalmanově filtru.

---

###  5: Ekonomické riziko chyb měření
**Otázka:** Co hrozí, když do modelu přidáš „příliš velké“ chyby měření (např. odhadneš jejich rozptyl jako volný parametr bez omezení)? Co pak model dělá?

> [!check]- Řešení a detailní vysvětlení
> **Tvoje odpověď:** *„Užívám tak záplatu, ale uměle zvyšuju náhodnost, což může být v rozporu s realitou.“*
> 
> **Hodnocení:** **Správná intuice, ekonomický důsledek je ještě závažnější.**
> 
> **Konkrétní riziko (tzv. "vysátí rozptylu"):**
> - Pokud necháš rozptyl chyby měření zcela volný, optimalizační algoritmus často zjistí, že nejjednodušší způsob, jak napasovat model na data, je **přiřknout veškerou variabilitu dat této chybě měření** ($\eta_t$).
> - **Výsledek:**
>   - Reálné strukturální šoky (technologie, preference) se scvrknou k nule.
>   - Model v podstatě řekne: *„Ekonomická teorie v modelu nevysvětluje nic, všechno je to jen statistický šum a bordel v datech.“*
> - **Doporučená praxe:** Rozptyl chyby měření se obvykle shora omezuje (např. apriorním rozdělením tak, aby chyba nevysvětlovala více než $10\text{--}20\,\%$ celkového rozptylu dané proměnné).