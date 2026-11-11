[Ben Lambert](https://www.youtube.com/watch?v=U561HGMWjcw)
→ K vzorkování z posterioru bez nutnosti počítat normalizační konstantu 
- Markov chain prvního řádu bo rozhodnutí o kam se pohnout závisí jen na součas pozici (ne na historii pozic)
- Monte Carlo - užívá generátor pseudorandom čísel

Shrnutí intuitivně : 
	→ o přijmutí rozhoduje zda kandidát má vyšší hustotu věrohodnoti než součas bod a pokud ne tak se přijme s pravděpodobností dle toho o kolik nižší tu hustotu pravděpodobnosti má než současný bod ne
## Algoritmus
### Krok 0: Inicializace ($t = 0$)
Zvolíme počáteční bod $\theta_0$ z libovolného rozdělení $\pi(\theta)$.  
*(V praxi může jít o náhodný odhad; algoritmus po čase zkonverguje, i když start není ideální).*

---

### Krok 1: Návrh kandidáta ($\theta_t'$)
Pro každou iteraci $t$ navrhneme novou pozici $\theta_t'$ ze **symetrického návrhového (skokového) rozdělení** centrovaného kolem současné pozice $\theta_{t-1}$:

$$\theta_t' \sim \mathcal{N}(\theta_{t-1}, \sigma^2)$$

* **Podmínka symetrie:** Pravděpodobnost navržení skoku z bodu $A$ do bodu $B$ musí být stejná jako z $B$ do $A$:
  $$P(\theta_A \to \theta_B) = P(\theta_B \to \theta_A)$$
  Normální rozdělení se středem v $\theta_{t-1}$ tuto vlastnost přirozeně splňuje - bo N rozdělení je symetrické : 
* Pravděpodobnost, že z bodu $A$ navrhneš skok o vzdálenost $+d$, je z vlastnosti symetrie normálního rozdělení kolem své střední hodnoty úplně stejná jako pravděpodobnost, že z bodu $B$ navrhneš skok o vzdálenost $-d$ zpět do $A$.
* Rozptyl $\sigma^2$ (velikost kroku / *step size*) volí uživatel.

---

### Krok 2: Výpočet akceptačního poměru ($r$)
Porovnáme výšku nenormalizovaného posterioru v navrženém bodě $\theta_t'$ s výškou v aktuálním bodě $\theta_{t-1}$:

$$r = \frac{P(X \mid \theta_t') \cdot P(\theta_t')}{P(X \mid \theta_{t-1}) \cdot P(\theta_{t-1})} = \frac{\text{výška posterioru v novém bodě}}{\text{výška posterioru v současném bodě}}$$

---

### Krok 3: Rozhodnutí o přijetí / zamítnutí
Pravděpodobnost příjmutí kandidát bodu je dáná akceptač. poměrem = a =min(1,r) → pokud má kandidát nižší hustotu pravděpodobnosti než součas bod tak ho příjmu s pravděpodobností takovou o kolik má nižší hustotu pravděpodobnosti než součas bod 
→ npř kandidát má r = 0.7 → príjmu ho s 70% pravděpodobností → k tomu pc generuje random číslo u z uniforum rozdělení (0,1)
→ při r>=1 se kandidát automaticky příjme (má vyšší PDF než součas bod → min(1,r)) = přijme se s 100% pravděpodobností

Vygenerujeme náhodné číslo z rovnoměrného rozdělení $u \sim \text{Unif}(0, 1)$ a porovnáme ho s $r$:

$$\text{Pokud } r > u \implies \text{\textbf{PŘIJMOUT:}} \quad \theta_t = \theta_t'$$
$$\text{Pokud } r \le u \implies \text{\textbf{ZAMÍTNOUT:}} \quad \theta_t = \theta_{t-1}$$

 #### **Intuitivní pravidla:**
1. **Krok do kopce ($r \ge 1$):**  
   Nová pozice má vyšší hustotu než stará. Protože $u \in (0, 1)$, nerovnost $r > u$ platí **vždy** $\implies$ **krok je 100% přijat**.
2. **Krok z kopce ($r < 1$):**  
   Nová pozice má nižší hustotu. Krok je přijat **pouze s pravděpodobností rovnou $r$** (např. při $r = 0{,}74$ je 74% šance na přijetí).
3. **Při zamítnutí:**  
   Zůstáváme na místě a **aktuální hodnotu zapíšeme do výsledného vzorku znovu** ($\theta_t = \theta_{t-1}$). V daném bodě tedy vzniká vícenásobný vzorek.

---

## Konvergence
* **$n = 100$:** Řetězec prozkoumal pouze bezprostřední okolí startovního bodu, tvar rozdělení vůbec neodpovídá PDF jež se snažím aproximovat.
* **$n = 2\,500$ až $9\,000$:** Vzorky začínají vykreslovat hlavní vrcholy real PDF.
* **$n = 20\,000$:** Rekonstruovaná hustota ze vzorků téměř dokonale odpovídá skutečné PDF

> **Asymptotická konvergence:**  
> Tyto podmínky zaručují ergodicitu MCMC - čili že asymptoticky konverguje k správnému rozdělení
> 	1. irreducibility 				→   RMW neuvízne v nějaké části prostoru a prozkoumá celý pravděpodobnostní prostor
> 	2. aperiodicity  				→   nesmí přecházet mezi stavy v pravidelných cyklech - u RMW splňeno bo u je random
> 	3. pozitivní rekurence 	→   řetězec se může toulat po stavovém prostoru, ale neztrácí se „někde v nekonečnu“ – vrací se do relevantních oblastí dostatečně často.

---

## ukázka 2D

Představme si terén odpovídající křivce posterioru (osa $x = \theta$, osa $y = \text{hustota}$):

```text
       Výška (Likelihood x Prior)
           ▲
           │          ▲ (vrchol 2)
           │   ▲     / \
           │  / \   /   \
           │ /   \_/     \
         ──┴───────────────► Parametr θ
```

Ve videu jsou demonstrovány 3 konkrétní iterace:

```text
Iterace 1 (Krok z kopce -> ZAMÍTNUTO):
  - Výška klesla z 1.2 na cca 0.7  ===>  r = 0.58
  - Vygenerováno náhodné u = 0.82
  - Test: r > u  (0.58 > 0.82) ? NE
  ===> ZAMÍTNUTO: Postavička zůstává stát, do vzorku se znovu zapíše původní bod.

Iterace 2 (Krok do kopce -> PŘIJATO):
  - Nový bod leží výše než současný  ===>  r > 1
  - Test: r > u platí vždy (protože u < 1)
  ===> PŘIJATO: Postavička se přesouvá na novou pozici.

Iterace 3 (Mírný sestup -> PŘIJATO):
  - Mírný pokles výšky  ===>  r = 0.74 (74% šance)
  - Vygenerováno náhodné u = 0.28
  - Test: r > u  (0.74 > 0.28) ? ANO
  ===> PŘIJATO: Postavička se i přes pokles posouvá na novou pozici.
```

---

## Ukázka v 3D (vrstevnicová mapa)

viz [vizualizace](https://chi-feng.github.io/mcmc-demo/app.html?algorithm=RandomWalkMH&target=banana)

* **Zelená čára:** Přijatý návrh (pohyb do nového místa).
* **červená čára:** Zamítnutý návrh (krok se neuskuteční, zůstává se na místě).

Po mnoha krocích začne trajektorie kopírovat tvar terénu – řetězec tráví nejvíce času kolem vrcholů s nejvyšší hustotou pravděpodobnosti.

## Vliv velikosti kroku ($\sigma$) v praxi

Při konečném počtu vzorků závisí efektivita na volbě směrodatné odchylky skokového rozdělení $\sigma$:

```text
Příliš malé σ:
[•]─[•]─[•]
  • Téměř každý krok je přijat (r ≈ 1).
  • Řetězec se však pohybuje extrémně pomalu a neprozkoumá celý prostor.

Příliš velké σ (obří skoky):
[•]───────────────────────────────► [x] (mimo rozdělení)
  • Návrhy často dopadají do oblastí s nulovou hustotou.
  • Téměř všechny návrhy jsou zamítnuty, řetězec dlouho stojí na místě.

Optimální σ:
  • Vyvážený poměr mezi přijímáním nových stavů a dostatečně rychlým průzkumem prostoru.
```

---

## 8. Shrnutí

| Výhody | Nevýhody |
| :--- | :--- |
| **Univerzálnost:** Lze aplikovat na téměř jakýkoliv model. | **Neefektivita:** Velké procento kroků je zamítnuto (zejména ve vyšších dimenzích). |
| **Jednoduchost:** Nepotřebuje derivace ani znalost normalizační konstanty. | **Nutnost ladění:** Je třeba manuálně nastavit vhodnou velikost kroku $\sigma$. |
| **Garantovaná konvergence:** V limitě ($n \to \infty$) vždy najde správné rozdělení. | Modernější metody (např. *HMC / NUTS*) využívající gradienty jsou pro složité úlohy řádově rychlejší. |