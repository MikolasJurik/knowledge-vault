Teď už máte kompletně rozebraných **5 zásadních článků**, které dohromady tvoří špičkovou rešerši na úrovni výborné diplomky:

1. **Little (1988)** – klasický test průměrů (naniar::mcar_test)
    
2. **Jamshidian & Jalal (2010)** – parametrický + neparametrický test kovariancí (MissMech::TestMCARNormality)
    
3. **Spohn et al. (2025)** – ML test celých distribucí na projekcích (PKLMtest)
    
4. **Berrett & Samworth (2023)** – teoreticky optimální test kompatibility (MCARtest)
    
5. **Molenberghs et al. (2008)** – důkaz nemožnosti odlišit MAR vs. MNAR
    

**Tip na skvělý „přidaný bonus“ do vaší diplomky (který vychází přímo z tohoto 5. článku):**

- Všimněte si Přílohy C (**Yuan et al., 2018**) a Tabulky 5 (kde Spohn et al. vyhodili kategoriální/faktorové proměnné): vy můžete ve své simulaci otestovat jak **Yuanův příklad** (stejný průměr i rozptyl
    
            `→→`
          
    
    Little i JJ selžou, PKLM vyhraje), tak **smíšená data** (např. příjem + věk + vzd

Všimněte si, jak dokonale se vám teď uzavřel kruh mezi **Little (1988)**, **Jamshidian & Jalal (2010)**, **Yuan et al. (2018)** a **Spohn et al. (2025)**:

Do své Monte Carlo simulace v R můžete zařadit **4 jasně odůvodněné typy mechanismů chybění** (přesně podle přečtené literatury):

1. **MCAR**
    
            `→→`
          
    
    ověření chyby I. druhu (zde čekáte inflaci u MissMech pro šikmá/těžkochvostá data a držení hladiny u PKLMtest a MCARtest).
    
2. **Lineární MAR (posun průměru)**
    
            `→→`
          
    
    klasický scénář, kde zazáří rychlý Little (naniar::mcar_test), ale i PKLMtest.
    
3. **Symetrický 5-pásmový MAR (Yuan et al., 2018, Příklad 1)**
    
            `→→`
          
    
    geniální zátěžový test převzatý z tohoto článku! Průměry i kovariance zůstávají stejné, takže Little (a z principu momentů i MissMech NP?) by měly selhat, zatímco PKLMtest (a diskretizovaný MCARtest) musí zachytit změnu tvaru rozdělení!
    
4. **MNAR (dva sub-scénáře):**
    
    - 4a: MNAR závislý přímo na hodnotě proměnné
        
                `X2X2​`
              
        
        při korelaci
        
                `ρ≠0ρ=0`
              
        
                `→→`
              
        
        jak velkou sílu mají testy OAR/MCAR nepřímo přes korelaci?
        
    - 4b: MNAR závislý na ortogonální složce
        
                `z2z2​`
              
        
        (Yuan et al., 2018, Věta 1)
        
                `→→`
              
        
        empirická demonstrace mezí všech testů (síla
        
                `≈5%≈5%`
              
        
        , v souladu s Molenberghs et al. 2008, Rhoads 2012 a Berrett & Samworth 2023).
        

Když tento design ukážete vedoucímu v Tezích DP, bude nadšený – každý jednotlivý scénář simulace je totiž přímo podložený konkrétním publikovaným článkem! Chcete už sepsat čistopis **Tezí DP** pro vedoucího, nebo máte ještě jeden článek (třeba mice::ampute – Schouten et al. 2018)?