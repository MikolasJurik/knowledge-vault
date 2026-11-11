

> **Hlavní myšlenka:** BK podmínky určují, zda má lineární dynamický model s racionálními očekáváními jednoznačné stabilní řešení.

Používají se zejména při řešení a analýze DSGE modelů. Kontrolují, zda existuje právě jedna trajektorie ekonomiky, která splňuje rovnice modelu, počáteční podmínky a podmínku neexplozivního vývoje.

## 1. Proč BK podmínky potřebujeme?

Uvažujme dynamický ekonomický model, který obsahuje dva typy proměnných:

**1. Predeterminované proměnné (predetermined variables)**

- Jejich současná hodnota je určena minulým vývojem.
- Nemohou se libovolně přizpůsobit novým informacím.
- Příkladem je kapitálový stav (K_t), jehož současná hodnota vychází z předchozí akumulace.
    

**2. Dopředné proměnné (forward-looking / jump variables)**

- Jejich současná hodnota může reagovat na očekávání budoucího vývoje.
- Mohou se přizpůsobit okamžitě, například po neočekávaném ekonomickém šoku.
- Příkladem může být spotřeba (C_t) nebo inflace (\pi_t), podle konkrétního modelu.
    

### Problém

Dynamické modely mohou mít více matematicky přípustných trajektorií.

Ekonomická teorie však často vyžaduje, aby očekávaný vývoj ekonomiky nebyl explozivní, tedy aby se systém nechoval způsobem, který odporuje předpokládanému ekonomickému omezení.

Potřebujeme proto určit:

- Existuje stabilní řešení?
    
- Je toto řešení jednoznačné?
    
- Dokážou se dopředné proměnné přizpůsobit tak, aby eliminovaly nestabilní vývoj?
    

Právě na tyto otázky odpovídají BK podmínky.

---

## 2. Matematický základ – vlastní čísla a stabilita

Po linearizaci modelu můžeme jeho dynamiku zjednodušeně reprezentovat:

[  
x_{t+1}=Ax_t  
]

Kde:

- (x_t) = vektor stavových proměnných,
    
- (A) = matice dynamického systému,
    
- (x_{t+1}) = stav systému v následujícím období.
    

Stabilita tohoto systému závisí na **vlastních číslech (eigenvalues)** matice (A).

### Stabilní a nestabilní vlastní čísla

|Vlastní číslo|Charakteristika|Chování|
|---|---|---|
|(|\lambda|<1)|Stabilní|Vliv počáteční odchylky postupně mizí|
|(|\lambda|>1)|Nestabilní|Odchylka se postupně zvětšuje|
|(|\lambda|=1)|Jednotkový kořen|Hraniční případ, vyžaduje zvláštní pozornost|

Například:

[  
x_{t+1}=0.8x_t  
]

Při počáteční hodnotě (x_0=100):

[  
100 \rightarrow 80 \rightarrow 64  
\rightarrow 51.2 \rightarrow \dots  
]

Systém konverguje k nule.

Naopak:

[  
x_{t+1}=1.2x_t  
]

[  
100 \rightarrow 120 \rightarrow 144  
\rightarrow 172.8 \rightarrow \dots  
]

Systém exploduje.

**Důležité:** U vlastních čísel nás zajímá absolutní hodnota, nikoliv pouze jejich znaménko.

### Proč je to důležité pro DSGE?

Uvažujme model se stabilními i nestabilními vlastními směry.

Predeterminované proměnné nemůžeme libovolně nastavit, protože jejich současné hodnoty jsou již určeny minulostí.

Dopředné proměnné se ale mohou přizpůsobit okamžitě.

Díky tomuto přizpůsobení mohou eliminovat nestabilní složky dynamiky a zajistit neexplozivní trajektorii.

To je základní intuice BK podmínek.

---

## 3. První BK podmínka – počtová podmínka (eigenvalue condition)

První podmínka porovnává:

1. Počet nestabilních vlastních čísel.
    
2. Počet dopředných (jump) proměnných.
    

Označme:

[  
N_u = \text{počet nestabilních vlastních čísel}  
]

[  
N_j = \text{počet dopředných proměnných}  
]

Pro existenci jednoznačného stabilního řešení musí platit:

[  
\boxed{N_u=N_j}  
]

### Proč musí být počty stejné?

Představ si, že model obsahuje dva nestabilní vlastní směry.

Aby bylo možné eliminovat oba nestabilní směry, potřebujeme dvě odpovídající možnosti okamžitého přizpůsobení prostřednictvím dopředných proměnných.

**Případ A: Správný počet**

- 2 nestabilní vlastní čísla
    
- 2 dopředné proměnné
    

Počtová podmínka je splněna.

**Případ B: Příliš mnoho nestabilních vlastních čísel**

- 3 nestabilní vlastní čísla
    
- 2 dopředné proměnné
    

Nemáme dostatek dopředných proměnných k eliminaci všech nestabilních směrů.

Výsledkem je zpravidla neexistence stabilního řešení.

**Případ C: Příliš málo nestabilních vlastních čísel**

- 1 nestabilní vlastní číslo
    
- 2 dopředné proměnné
    

Existuje příliš mnoho možností přizpůsobení. Model může mít nekonečně mnoho stabilních řešení.

Tento problém se označuje jako **indeterminace (indeterminacy)**.

### Přehled

|Nestabilní vlastní čísla|Jump proměnné|Výsledek|
|---|---|---|
|2|2|Počtová podmínka splněna|
|3|2|Neexistence stabilního řešení|
|1|2|Indeterminace – více řešení|

Pozor: Splnění počtové podmínky ještě automaticky nezaručuje jednoznačné řešení!

---

## 4. Druhá BK podmínka – podmínka hodnosti (rank condition)

Druhá podmínka řeší problém, který samotné počítání vlastních čísel nedokáže zachytit.

Nestačí, že máme správný počet dopředných proměnných. Musí také existovat dostatečně nezávislá vazba mezi těmito proměnnými a nestabilními vlastními směry.

### Intuice

Představ si:

- 2 nestabilní vlastní směry,
    
- 2 dopředné proměnné – spotřebu a inflaci.
    

Počtová podmínka je splněna.

Co když ale obě dopředné proměnné působí na nestabilní část systému lineárně závislým způsobem?

Pak nemusí být možné pomocí jejich přizpůsobení nezávisle eliminovat oba nestabilní směry.

Potřebujeme proto ověřit **plnou hodnost (full rank)** příslušné matice.

### Matematické vyjádření

V klasickém vyjádření BK se po rozložení dynamického systému na stabilní a nestabilní vlastní směry získá transformační matice:

[  
C=  
\begin{pmatrix}  
C_{11} & C_{12}\  
C_{21} & C_{22}  
\end{pmatrix}  
]

Řádky matice jsou rozděleny podle stabilních a nestabilních vlastních směrů, sloupce podle predeterminovaných a dopředných proměnných.

Matice (C_{22}) tedy popisuje vazbu mezi dopřednými proměnnými a nestabilními kanonickými směry.

Podmínka hodnosti je:

[  
\boxed{\operatorname{rank}(C_{22})  
=\min(N_u,N_j)}  
]

V případě, kdy je splněna také počtová podmínka (N_u=N_j), je (C_{22}) čtvercová matice a musí být regulární (invertibilní):

[  
\boxed{\det(C_{22})\neq 0}  
]

To znamená, že vazby mezi dopřednými proměnnými a nestabilními směry jsou dostatečně nezávislé.

### Názorná ilustrace

Představ si, že zkoumáme matici vazeb mezi dvěma dopřednými proměnnými a dvěma nestabilními směry.

**Příklad 1 – plná hodnost:**

[  
M=  
\begin{pmatrix}  
1&0\  
0&1  
\end{pmatrix}  
]

[  
\operatorname{rank}(M)=2  
]

Obě vazby jsou lineárně nezávislé.

**Příklad 2 – nedostatečná hodnost:**

[  
M=  
\begin{pmatrix}  
1&2\  
2&4  
\end{pmatrix}  
]

Druhý řádek je dvojnásobkem prvního:

[  
\operatorname{rank}(M)=1  
]

Přestože máme dvě dopředné proměnné a dva nestabilní směry, matice nemá plnou hodnost.

<mark>Počtová podmínka je splněna, ale podmínka hodnosti nikoliv.</mark>

Poznámka: Tyto dvě matice představují ilustraci principu hodnosti, nikoliv automaticky konkrétní matici (C_{22}) vypočtenou z určitého DSGE modelu.

---

## 5. Příklad – proč nestačí pouze počítat vlastní čísla?

Uvažujme velmi jednoduchý model:

[  
\begin{aligned}  
x_{t+1}&=1.2x_t\  
p_{t+1}&=0.8p_t  
\end{aligned}  
]

Předpokládejme:

- (x_t) je predeterminovaná proměnná.
    
- (p_t) je dopředná proměnná.
    

Matice dynamického systému je:

[  
A=  
\begin{pmatrix}  
1.2&0\  
0&0.8  
\end{pmatrix}  
]

Vlastní čísla:

[  
\lambda_1=1.2,\qquad \lambda_2=0.8  
]

Máme tedy:

- 1 nestabilní vlastní číslo.
    
- 1 dopřednou proměnnou.
    

Počtová podmínka je splněna.

Jenže dopředná proměnná (p_t) vůbec neovlivňuje nestabilní proměnnou (x_t).

Pro počáteční hodnotu (x_0\neq0) proto platí:

[  
x_t=1.2^t x_0  
]

a trajektorie exploduje bez ohledu na hodnotu (p_t).

V transformační matici, která odděluje stabilní a nestabilní směry, dostaneme:

[  
C=  
\begin{pmatrix}  
0&1\  
1&0  
\end{pmatrix}  
]

Stabilní vlastní směr je první řádek, nestabilní druhý. Sloupce odpovídají proměnným (x_t,p_t).

Z toho vychází:

[  
C_{22}=0  
]

[  
\operatorname{rank}(C_{22})=0  
]

Podmínka hodnosti není splněna.

**Závěr:** Správný počet dopředných proměnných ještě neznamená, že tyto proměnné skutečně dokážou kontrolovat nestabilní dynamiku.

---

## 6. Co nastane, když BK podmínky nejsou splněny?

|Počtová podmínka|Rank condition|Interpretace|
|---|---|---|
|Splněna|Splněna|Jednoznačné stabilní řešení|
|Nesplněna – příliš mnoho nestabilních kořenů|–|Zpravidla neexistuje stabilní řešení|
|Nesplněna – příliš málo nestabilních kořenů|–|Více stabilních řešení (indeterminace)|
|Splněna|Nesplněna|Počet nestačí k zajištění jednoznačného řešení|

Uvedené výsledky vycházejí ze standardního pravidelného případu BK. Degenerované případy a jednotkové kořeny vyžadují zvláštní zacházení.

---

## 7. Praktické použití v DSGE modelech

V praxi se BK podmínky zpravidla ověřují numericky.

Typický postup:

1. Sestavení DSGE modelu.
    
2. Výpočet steady state.
    
3. Linearizace modelu kolem steady state.
    
4. Výpočet vlastních čísel, případně zobecněných vlastních čísel.
    
5. Kontrola počtové podmínky.
    
6. Kontrola rank condition.
    

### Schurova dekompozice (QZ decomposition)

U větších DSGE modelů se běžně používá zobecněná Schurova dekompozice neboli QZ decomposition.

Ta umožňuje rozdělit dynamický systém na stabilní a nestabilní části a numericky ověřit BK podmínky.

Není tedy nutné ručně počítat všechna vlastní čísla a vlastní vektory pomocí Jordanovy formy.

### Dynare

V programu Dynare lze zkontrolovat vlastní čísla pomocí příkazu:

```
steady;
check;
```

Výstup obsahuje zobecněná vlastní čísla, jejichž absolutní hodnoty umožňují určit stabilní a nestabilní kořeny.

Dynare následně ověřuje také podmínku hodnosti.

---

## 8. Vztah ke stochastické singularitě

BK podmínky a stochastická singularita jsou dva odlišné problémy.

|BK podmínky|Stochastická singularita|
|---|---|
|Zkoumají dynamické řešení modelu|Zkoumá lineární závislosti ve stochastické části modelu či pozorovaných veličin|
|Vlastní čísla a vlastní směry|Kovarianční matice a její hodnost|
|Existence a jednoznačnost stabilního řešení|Dostatečný počet nezávislých zdrojů variability|

Model může splňovat BK podmínky a přesto být stochasticky singulární.

Splnění BK podmínek proto samo o sobě nezaručuje, že model lze úspěšně odhadnout na pozorovaných datech.

---

## 9. Co si zapamatovat ke zkoušce?

**První BK podmínka – početní:**

Má model správný počet nestabilních vlastních čísel vzhledem k počtu dopředných proměnných?

[  
N_u=N_j  
]

**Druhá BK podmínka – hodnostní:**

Jsou vazby mezi nestabilními vlastními směry a dopřednými proměnnými dostatečně nezávislé?

[  
\operatorname{rank}(C_{22})=N_j=N_u  
]

**Výsledný princip:**

Dopředné proměnné musí mít dostatečnou možnost přizpůsobit se tak, aby existovala právě jedna stabilní trajektorie splňující ekonomické rovnice a počáteční podmínky.

---

## 10. Doporučené zdroje

1. [Blanchard & Kahn (1980) – The Solution of Linear Difference Models under Rational Expectations](https://people.bu.edu/rking/SZGcourse/blanchardkahnem80.pdf) – původní článek, ve kterém jsou BK podmínky odvozeny.
    
2. [Dynare – Solutions and Simulations](https://archives.dynare.org/documentation-and-support/faq/solutions-simulations.html) – praktická interpretace BK podmínek a jejich kontroly v Dynare.
    
3. [Klein (2000) – Using the Generalized Schur Form to Solve a Multivariate Linear Rational Expectations Model](https://doi.org/10.1016/S0165-1889\(99\)00045-7) – numerické řešení pomocí QZ dekompozice.
    

### Předpoklady pro pochopení

- [[Vlastní čísla a vlastní vektory]]
    
- [[Hodnost matice]]
    
- [[Lineární nezávislost]]
    
- [[Stabilita dynamických systémů]]
    
- [[Racionální očekávání]]
    
- [[DSGE modely]]
    
- [[Linearizace modelů]]