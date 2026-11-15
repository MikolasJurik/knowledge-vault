
## 4. Identifikace modelu na datech
### a) Příprava
Kvůli problému stochastické singularity je nutno užít minimálně stejně šoků jako pozorovaných proměných. V modelu model7_linear.mod je poze jeden šok (TFP), proto k identifikaci modelu může (být bez přidání chyb měření) užita pouze jedna pozorovaná proměná. Technologický šok zasahuje celou ekonomiku rovnoměrně, proto mu nejlépe bude odpovídat proměná dy (změna výstupu). K získání více informací je také vhodné zahrnout proměné změnu spotřeby a investic (dc a di). To ale kvůli stochastické singulatitě implikuje nutnost zahrnout pro tyto proměné chyby měření jako další šoky. U makroekonomických proměných lze očekávat chyby měření, jelikož jde o agregace a k chybám může dojít npř. při sezoním očišťování. či statistických revizí. Model bude proto odhadnut na 3 pozorovaných proměných (dy,di,dc) s třemi šoky (e,me_i,me_c) kde dy slouží jako referenční veličina přímo identifikující  strukturální šok e. Naopak úroková sazba (`robs`) nebyla zařazena, protože model 7 je modelem bez monetární politiky,  Veličina r představuje proto  reálnou míru pronájmu fyzického kapitálu, nikoliv  úrokovou sazbu z dat. Měřící rovnice pak budou: 
  $$\text{dy}_t = y_t - y_{t-1}, \qquad \text{dc}_t = c_t - c_{t-1} + \varepsilon^{me,c}_t, \qquad \text{di}_t = i_t - i_{t-1} + \varepsilon^{me,i}_t.$$

### b) Kalibrace, estimace

Kalibrovány byly parametry určující dlouhodobý ustálený stav a institucionální nastavení, které nelze z cyklických dat temp růstu bez úrovňových či fiskálních řad spolehlivě identifikovat:
  * **`beta = 0,99` a `delta = 0,025`:** Standardní čtvrtletní kalibrace, která je konzistentní se čtvrtletní frekvencí datasetu `DATA_DU.mat` (200 pozorování).
  * **`tauc = 0,116`, `taul = 0,344`, `tauk = 0,225`:** Efektivní daňové sazby převzaté z literatury (Torres, 2016) jako exogenní parametry fiskální politiky.
  * **`gamma = 0,40`:** Váha spotřeby  v užitkové funkci určující ustálenou nabídku práce (bez pozorované řady odpracovaných hodin by byla slabě identifikovatelná)

Zbylé parametry byly baynesiansky odhadnuty z dat: 
  * **`alpha`** (podíl kapitálu na produktu): `Beta(0,35; 0,05)` → Beta rozdělení omezuje parametr na teoreticky přípustný interval $(0,1)$, průměr `0,35` odpovídá standardnímu podílu kapitálu.
  * **`rho`** (perzistence TFP šoku): `Beta(0,85; 0,10)` → Beta rozdělení zaručuje stacionaritu AR(1) procesu  s  očekáváním vyšší perzistence čtvrtletních technologických šoků.
  * **`stderr e`** (std TFP šoku): `InvGamma(0,01; Inf)` – inverzní Gama rozdělení zaručuje kladnost směrodatné odchylky; průměr `0,01` odpovídá 1% šoku v desetinném vyjádření a nekonečná směrodatná odchylka představuje málo informativní prior.
  * **`stderr me_c` a `stderr me_i`** (std chyb měření): `InvGamma(0,005; Inf)` pro spotřebu a `InvGamma(0,01; Inf)` pro investice – odráží empiricky nižší volatilitu spotřeby oproti investicím.


### c) RWMH, konvergence, srovnání priorů s posteriory
Pro nalezení modu posterioru byl užit mode_compute = 6 čili Monte Carlo optimalizátor kvůli jeho vyšší robustnosti.
V rámci RWMH byli zkonstruovány 2 markovovy řetězce s 40000 replikacemi a s měřítkem kroku mh_jscale = 1,1. Tyto hodnoty byli zvoleny aby bylo dosaženo výsledné akceptační pravděpodobnosti 32,68 % respektive 33,09 % pro druhý řetězec. Tyto hodnoty spadají do doporučovaného pásma akceptační pravděpodobnosti jež je mezi 20% až 40%. Burn-in byl nastaven na mh_drop = 0,5 čili bylo vyřazeno 50% prvních iterací.

Oba řetězce se pro všechny odhadované proměné, po vynechání burn-in části, překrývají a konvergují ke shodným posteriorním hodnotám na 3 až 4 desetinná místa.  Formální test lokální identifikovatelnosti v apriorním průměru (identification(ar=1)) potvrzuje, že ......... To potvrzuje i srovnání apriorních a aposteriorních rozdělení ....…“

Všech 5 parametrů je dobře identifikovatelných z dat (posteriorní hustoty jsou výrazně užší a vyšší než široké šedé apriorní hustory). U všech došlo ke znatelnému zúžení posteriorního rozdělení oproti apriornímu (data jsou informativnější než prior):

| Parametr           | Prior (průměr / std) | Post. mode (s.d.) | Post. mean | 90% HPD interval |
| :----------------- | :------------------: | :---------------: | :--------: | :--------------: |
| $\alpha$ (`alpha`) |  Beta(0,350; 0,050)  |  0,3413 (0,0035)  |   0,3415   | [0,3347; 0,3477] |
| $\rho$ (`rho`)     |  Beta(0,850; 0,100)  |  0,8381 (0,0071)  |   0,8364   | [0,8171; 0,8573] |
| `stderr e`         |   InvG(0,010; Inf)   |  0,0084 (0,0004)  |   0,0085   | [0,0077; 0,0092] |
| `stderr me_c`      |   InvG(0,005; Inf)   |  0,0020 (0,0001)  |   0,0020   | [0,0018; 0,0021] |
| `stderr me_i`      |   InvG(0,010; Inf)   |  0,0064 (0,0003)  |   0,0064   | [0,0059; 0,0070] |
![[Pasted image 20261115204942.png|661]]![[Pasted image 20261115202335.png|711]]
*Obr. 1: multivariační konvergenční diagnostika*

![[Pasted image 20261115204916.png]]

*Obr. 2: Porovnání priorů a posteriorů: šedá = prior; černá = posterior; zelená = posteriorní mode*

### d) Baynesianské IRF
Na Obr. 3 jsou zobrazeny bayesiánské impulzní odezvy na kladný technologický šok o velikosti jedné směrodatné odchylky (cca  0,85 % )
 - **Výstup ($Y$):** Na dopad šoku okamžitě vzroste vlivem vyšší TFP i nárůstu odpracovaných hodin a díky vysoké perzistenci $\rho = 0{,}836$ i postupné akumulaci kapitálu se vrací zpět k ustálenému stavu pozvolna.
* **Investice ($I$):** Reagují ze všech sledovaných veličin nejprudčeji. Vyšší produktivita zvedá očekávaný mezní výnos kapitálu na delší dobu dopředu, což motivuje k intenzivní akumulaci kapitálu; po prvotním skoku investice plynule klesají zpět k rovnováze.
* **Spotřeba ($C$):** Vykazuje nejhladší a nejperzistentnější reakci v souladu s principem vyhlazování spotřeby.  Spotřeba vzroste na dopad, dále postupně kulminuje se zpožděním (v důsledku akumulace kapitálu) a zůstává kladná po celý sledovaný horizont. 
* **Práce ($L$):** V 1. čtvrtletí vzroste, protože nárůst reálné mzdy vyvolává substituční efekt, který převáží nad důchodovým efektem. Jak šok postupně odeznívá a zásoba kapitálu i spotřeba zůstávají zvýšené, nabídka práce klesá zpět k ustálenému stavu.
* **Vliv daní a nejistota odhadu:** Zkreslující daně ($\tau_c, \tau_l, \tau_k$) tlumí celkovou sílu expanze (daň z práce a spotřeby snižuje čistou motivaci k růstu $L$, zdanění kapitálu snižuje čistý výnos a brzdí investiční reakci $I$). 
* Úzká 90% HPD pásma spolehlivosti kolem průměrné odezvy potvrzují přesný odhad dynamických parametrů.

![[Pasted image 20261115204840.png]]

*Obr. 3:* Baynesianké IRF

![[Pasted image 20261115205039.png]]
![[Pasted image 20261115205022.png]]
















Pozitivní technologický šok o velikosti jedné odhadnuté směrodatné odchylky (~1,25 %) vyvolá díky relativně nízké odhadnuté perzistenci ($\rho = 0{,}411$) krátkodobý, rychle odeznívající nárůst produktivity. To má zásadní vliv na chování všech čtyř proměnných:

1. Výstup ($Y$):
   - V 1. čtvrtletí okamžitě výrazně vzroste o cca 2 % (kombinace přímého efektu TFP  a nárůstu odpracovaných hodin).
   - S odezníváním šoku postupně klesá, zůstává však mírně kladný zásluhou vyšší zásoby akumulovaného kapitálu.

2. Investice ($I$):
   - Reagují na šok nejprudčeji ze všech proměnných – v 1. čtvrtletí vyskočí o cca 8%. Protože je nárůst produktivity dočasný, domácnosti chtějí krátkodobě vysokého mezního výnosu kapitálu využít a přelít většinu mimořádného produktu do akumulace kapitálu.
   - S odezníváním šoku investice prudce klesají a od 5. čtvrtletí  se dostanou mírně pod ustálený stav. Důvodem tohoto jevu může být to, že ekonomika mánadbytečný kapitál při již odeznělé produktivitě, což stlačuje mezní produkt kapitálu pod dlouhodobou rovnováhu. Investuje se proto dočasně investují méně, než kolik činí amortizace, dokud se kapitál nevrátí zpět do ustáleného stavu.

3. Spotřeba ($C$):
   - Vykazuje nejhladší průběh. Na dopad šoku vzroste jen mírně a svého vrcholu dosáhne se zpožděním ve 2. čtvrtletí  a poté klesá jen velmi pozvolna.

4. Práce ($L$):
   - V 1. čtvrtletí vzroste o +1,3 %. To lze interpretovat skrz to že domácnosti  chtějí pracovat více nyní, dokud je reálná mzda přechodně vysoká.
   - Po odeznění šoku nabídka práce rychle klesá a od 4. čtvrtletí se dostává mírně pod ustálený stav. V tu chvíli už technologický stimul vyprchal, ale domácnosti jsou bohatší, takže převažuje důchodový efekt a domácnosti preferují  o něco více volného času.
5. Role daní a nejistota odhadu (90% HPD interval):
   - Daně ($\tau_c, \tau_l, \tau_k$) celkovou velikost expanze tlumí (daňový klín u práce snižuje motivaci k nárůstu $L$, zdanění kapitálu snižuje čistý výnos a brzdí investiční reakci $I$).
   - Úzké 90% HPD pásmo spolehlivosti okolo průměrné odezvy odráží vysokou přesnost odhadnutých parametrů ($\alpha, \rho, \sigma_e$).








--------------------------------------------

---

### 4(a) Počet pozorovaných proměnných, výběr dat, chyby měření a rovnice měření

* **Počet pozorovaných proměnných:** Aby při vyhodnocování věrohodnostní funkce pomocí Kalmanova filtru nedošlo k problému **stochastické singularity**, nesmí počet pozorovaných proměnných převýšit počet exogenních stochastických šoků v modelu (strukturálních šoků + chyb měření). Protože model 7 obsahuje pouze **1 strukturální šok** (technologický TFP šok `e`), lze bez chyb měření použít nejvýše 1 pozorovanou proměnnou; po zavedení chyb měření však lze využít až všechny 4 řady z datasetu `DATA_DU.mat`.
* **Volba pozorovaných proměnných:** Protože jediným strukturálním šokem v modelu je nabídkový TFP šok, nejlépe jeho charakteru odpovídají reálné makroekonomické veličiny – **tempo růstu výstupu (`dy`)**, **tempo růstu spotřeby (`dc`)** a **tempo růstu investic (`di`)**. Ty v modelu přímo reagují na změny produktivity a zachycují rozdělení výstupu mezi spotřebu a akumulaci kapitálu. Naopak úroková sazba (`robs`) nebyla zařazena, protože model 7 je čistě reálný model bez centrální banky, inflace a finančních frikcí, takže modelový výnos kapitálu nereflektuje empirickou dynamiku měnověpolitické či tržní sazby.
* **Chyby měření:** U makroekonomických agregátů je rozumné očekávat chyby měření z důvodu statistických revizí, sezonního očišťování i metodického rozdílu mezi uzavřeným modelem (kde výstup je jen součtem spotřeby a investic) a daty z národních účtů. Z ekonometrického hlediska jsou navíc nutné pro odstranění stochastické singularity. Do rovnic měření byly proto přidány dvě nezávislé chyby měření (`me_c` u spotřeby a `me_i` u investic), čímž počet šoků (3) odpovídá počtu pozorovaných proměnných (3) a `dy` přímo kotví TFP šok.
* **Rovnice měření:** Jelikož proměnné `y, c, i` v log-linearizovaném modelu představují log-odchylky od ustáleného stavu a data v `DATA_DU.mat` jsou již centrovaná tempa růstu (průměry $\approx 0$) v desetinném vyjádření, mají rovnice měření tvar prvních diferencí bez konstanty:
  $$\text{dy}_t = y_t - y_{t-1}, \qquad \text{dc}_t = c_t - c_{t-1} + \varepsilon^{me,c}_t, \qquad \text{di}_t = i_t - i_{t-1} + \varepsilon^{me,i}_t.$$

---

### 4(b) Kalibrace vs. odhadované parametry a apriorní nastavení

* **Kalibrované parametry:** Fixovány byly parametry určující dlouhodobý ustálený stav a institucionální nastavení, které nelze z cyklických dat temp růstu bez úrovňových či fiskálních řad spolehlivě identifikovat:
  * **`beta = 0,99` a `delta = 0,025`:** Standardní čtvrtletní kalibrace (diskontní faktor odpovídající cca 4 % roční čisté úrokové míry a 10 % roční míře odpisů), která je konzistentní se čtvrtletní frekvencí datasetu `DATA_DU.mat` (200 pozorování).
  * **`tauc = 0,116`, `taul = 0,344`, `tauk = 0,225`:** Efektivní daňové sazby převzaté z literatury (Torres, 2016) jako exogenní parametry fiskální politiky.
  * **`gamma = 0,40`:** Váha volného času v užitku určující ustálenou nabídku práce $L_{ss}$ (bez pozorované řady odpracovaných hodin by byla slabě identifikovatelná).
  * *(Technická poznámka: Odvozené veličiny ustáleného stavu `R_ss, CY_ss, IY_ss, L_ss` atd. jsou definovány jako lokální proměnné modelu `#`, aby se v průběhu odhadu automaticky přepočítávaly při každé změně parametru `alpha`.)*
* **Odhadované parametry a apriorní rozdělení:**
  * **`alpha`** (podíl kapitálu na produktu): `Beta(0,35; 0,05)` – Beta rozdělení omezuje parametr na ekonomicky přípustný interval $(0,1)$, průměr `0,35` odpovídá standardnímu podílu kapitálu.
  * **`rho`** (perzistence TFP šoku): `Beta(0,85; 0,10)` – Beta rozdělení zaručuje stacionaritu AR(1) procesu ($\rho \in (0,1)$) s realistickým očekáváním vyšší perzistence čtvrtletních technologických šoků.
  * **`stderr e`** (std TFP šoku): `InvGamma(0,01; Inf)` – inverzní Gama rozdělení zaručuje kladnost směrodatné odchylky; průměr `0,01` odpovídá 1% šoku v desetinném vyjádření a nekonečná směrodatná odchylka představuje málo informativní (difúzní) prior.
  * **`stderr me_c` a `stderr me_i`** (std chyb měření): `InvGamma(0,005; Inf)` pro spotřebu a `InvGamma(0,01; Inf)` pro investice – odráží empiricky nižší volatilitu spotřeby oproti investicím.

---

### 4(c) Odhad pomocí RWMH, konvergence a srovnání priorů s posteriory

* **Algoritmy a nastavení MCMC:**
  * Řešení racionálních očekávání lineárního modelu bylo nalezeno perturbační metodou 1. řádu (QZ rozklad; Blanchard-Kahnovy podmínky jsou splněny – 2 vlastní čísla $> 1$ pro 2 dopředně hledící proměnné).
  * Pro nalezení posteriorního módu byl zvolen optimalizační algoritmus založený na metodě Monte Carlo (**`mode_compute = 6`**), který dosáhl hodnoty log-posterioru **`2268,77`** (optimální škálovací parametr `1,1116`).
  * Následné vzorkování posteriorního rozdělení proběhlo pomocí algoritmu **RWMH** se **`40 000` replikacemi** ve **`2` nezávislých řetězcích**, přičemž prvních **50 % iterací (`mh_drop = 0.5`)** bylo vyřazeno jako *burn-in* (ponecháno 20 000 výběrů na řetězec) a měřítko kroku bylo nastaveno na **`mh_jscale = 1.1`**.
* **Akceptační pravděpodobnosti:**
  * Řetězec 1 dosáhl akceptace **`31,25 %`** a řetězec 2 **`31,75 %`**. Obě hodnoty leží přímo v doporučeném optimálním rozmezí **20 %–40 %**, což značí dobře vyladěný krok návrhového rozdělení.
* **Hodnocení konvergence:**
  * Logaritmická marginální hustota dat z Laplaceovy aproximace (`2236,72`) a z modifikovaného harmonického průměru (`2238,18`) jsou si velmi blízké.
  * Průměry obou řetězců se prakticky shodují (`alpha`: `0,342` vs. `0,341`; `rho`: `0,835` vs. `0,837`; šoky totožné na 3 desetinná místa).
  * **Gewekův test:** V 1. řetězci nevykazuje statisticky významný posun průměru žádný parametr ($p$-hodnoty s vyhlazením `0,27–0,53 > 0,05`). Ve 2. řetězci procházejí všechny šoky ($p = 0{,}14\text{--}0{,}51$), zatímco u `alpha` a `rho` je $p$-hodnota při 4% okně na hranici (`0,047`), při 8% a 15% vyhlazení již přesahuje `0,05` (`0,087–0,100`). To souvisí s vyšším faktorem neefektivity (*inefficiency factor*) u `alpha` (`114–129`) a `rho` (`166–180`), které ukazují na vyšší autokorelaci výběrů v řetězcích; celkově jsou však odhady napříč řetězci konzistentní *(ve zprávě ještě potvrďte sblížením křivek v grafech Brooks-Gelman `udiag` a `mdiag`)*.
* **Porovnání apriorního a posteriorního rozdělení a identifikovatelnost:**
  * Všech 5 parametrů je dobře identifikovatelných – u všech došlo ke znatelnému zúžení posteriorního rozdělení oproti apriornímu (data jsou informativní):
    * **`alpha`:** z apriorního průměru `0,350` (std `0,050`) se zpřesnila na **`0,3416`** (`90% HPD [0,3352; 0,3485]`, std `0,004`).
    * **`rho`:** z apriorního průměru `0,850` (std `0,100`) na **`0,8358`** (`90% HPD [0,8164; 0,8574]`, std `0,012`) – data silně potvrzují vysokou perzistenci technologického šoku kolem `0,84`.
    * **`stderr e`:** z `0,010` na **`0,0085`** (`[0,0078; 0,0092]`).
    * **`stderr me_c`:** z `0,005` na **`0,0020`** (`[0,0018; 0,0021]`) a **`stderr me_i`:** z `0,010` na **`0,0064`** (`[0,0059; 0,0070]`). Chyba měření u investic je tedy zhruba třikrát vyšší než u spotřeby, což odpovídá výrazně vyšší volatilitě investic v datech.

---

### 4(d) Interpretace bayesiánských impulzních odezev (IRF)

Pozitivní technologický (TFP) šok o velikosti jedné odhadnuté směrodatné odchylky (`~0,85 %`) při vysoké odhadnuté perzistenci ($\rho = 0{,}836$) představuje **trvalejší (pomalu odeznívající) nárůst produktivity** (ještě po 4 čtvrtletích přetrvává zhruba polovina původního šoku):

* **Výstup ($Y$):** Na dopad šoku okamžitě vzroste vlivem vyšší TFP i nárůstu odpracovaných hodin a díky vysoké perzistenci $\rho = 0{,}836$ i postupné akumulaci kapitálu se vrací zpět k ustálenému stavu pozvolna v průběhu celého 20čtvrtletního horizontu.
* **Investice ($I$):** Reagují ze všech sledovaných veličin **nejprudčeji** (procentuálně násobně více než výstup). Vyšší produktivita zvedá očekávaný mezní výnos kapitálu na delší dobu dopředu, což motivuje k intenzivní akumulaci kapitálu; po prvotním skoku investice plynule klesají zpět k rovnováze.
* **Spotřeba ($C$):** Vykazuje nejhladší a nejperzistentnější reakci v souladu s principem **vyhlazování spotřeby (*consumption smoothing*)**. Protože je šok vysoce perzistentní, vnímají domácnosti nárůst permanentního důchodu silněji – spotřeba vzroste na dopad, dále postupně kulminuje se zpožděním (*hump-shaped* průběh v důsledku akumulace kapitálu) a zůstává kladná po celý sledovaný horizont.
* **Práce ($L$):** V 1. čtvrtletí vzroste, protože nárůst reálné mzdy vyvolává **substituční efekt** (práce je lépe odměněna), který převáží nad důchodovým efektem. Jak šok postupně odeznívá a zásoba kapitálu i spotřeba zůstávají zvýšené, nabídka práce klesá zpět k ustálenému stavu (v pozdějších čtvrtletích může vlivem důchodového efektu klesnout i mírně pod rovnováhu).
* **Vliv daní a nejistota odhadu:** Zkreslující daně ($\tau_c, \tau_l, \tau_k$) tlumí celkovou sílu expanze (daň z práce a spotřeby snižuje čistou motivaci k růstu $L$, zdanění kapitálu snižuje čistý výnos a brzdí investiční reakci $I$). Úzká 90% HPD pásma spolehlivosti kolem průměrné odezvy potvrzují přesný odhad dynamických parametrů.


### Rychlý tip pro doplnění přesných čísel do bodu 4(d)
Váš výpis neobsahoval čísla z matice `oo_.PosteriorIRF`, takže abyste do textu v bodě (d) mohl napsat i přesné procentní skoky z *tohoto* běhu (např. *"Výstup v 1. čtvrtletí vzrostl o X %"*), stačí v MATLABu po tomto běhu spustit jen výpis tabulky:

```matlab
table((1:8)', 100*oo_.PosteriorIRF.dsge.Mean.y_e(1:8), ...
    100*oo_.PosteriorIRF.dsge.Mean.c_e(1:8), ...
    100*oo_.PosteriorIRF.dsge.Mean.l_e(1:8), ...
    100*oo_.PosteriorIRF.dsge.Mean.i_e(1:8), ...
    'VariableNames', {'Q', 'Y_pct', 'C_pct', 'L_pct', 'I_pct'})
```
*(Pozn.: Také doporučuji před odevzdáním zkontrolovat graf `mode_check`, zda `mode_compute = 6` skončil přesně na vrcholu křivky, a v `.mod` souboru případně smazat nadbytečné řádky `// Analytical steady state` nad blokem `model(linear);`, protože je plně nahrazují `#` definice uvnitř bloku – výpočet to nijak neovlivní, ale kód bude ještě čistší.)*

ans =

  8×5 table

    Q     Y_pct      C_pct      L_pct     I_pct 
    _    _______    _______    _______    ______

    1     1.4516    0.22996    0.91664    5.6728
    2     1.2415    0.26205    0.73491    4.6262
    3     1.0647    0.28571    0.58454    3.7573
    4     0.9159    0.30247    0.46027    3.0364
    5    0.79038     0.3136    0.35773    2.4387
    6    0.68438    0.32016    0.27328    1.9438
    7    0.59472    0.32302    0.20386    1.5344
    8    0.51876     0.3229    0.14695    1.1964