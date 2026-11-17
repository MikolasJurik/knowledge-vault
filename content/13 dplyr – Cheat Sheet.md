
| Cíl | Funkce |
| :--- | :--- |
| **Vybrat řádky** podle podmínky / pozice | `filter()` / `slice()` |
| **Vybrat / přejmenovat / přesunout sloupce** | `select()` / `rename()` / `relocate()` *(doplněk)* |
| **Vytvořit / změnit sloupce** | `mutate()` |
| **Seřadit řádky** | `arrange()` |
| **Odstranit duplicity** | `distinct()` |
| **Shrnout tabulku** (agregace) | `summarise()`, `count()` |
| **Aplikovat funkci na více sloupců najednou** | `across()` (v `mutate`/`summarise`), `if_any()`/`if_all()` (v `filter`) |
| **Počítat po skupinách** | `group_by()` + nezapomenout na `ungroup()` |
| **Spojit tabulky podle klíče** | `*_join()`, `semi_join()`, `anti_join()` |
| **Slepit tabulky pod/vedle sebe** | `bind_rows()`, `bind_cols()` |

---

## 1. Slovesa pro jednu tabulku

###  Výběr řádků: `filter()`, `slice()`, `distinct()`, `arrange()`

#### `filter()` – výběr podle podmínky
Čárka `,` funguje jako **AND** (`&`), svislítko `|` jako **OR**.

```r
# Výrobce je v seznamu A ZÁROVEŇ má více než 2 motory
planes %>%
  filter(manufacturer %in% c("AIRBUS INDUSTRIE", "BOEING"), engines > 2)

# Podmínka přes výpočet ze sloupců
planes %>%
  filter(seats / engines < 10)

# Textová podmínka (stringr) – modely začínající na "A340"
planes %>%
  filter(str_detect(model, "^A340"))

# Negace + ochrana proti NA (nevalidní kód: není velké písmeno A NENÍ NA)
ispv %>%
  filter(!(VZDELANI %in% LETTERS), !is.na(VZDELANI))
```

> [!warning] Zkouškový chyták: `NA` v `filter()`
> - `filter()` **automaticky zahazuje řádky, kde podmínka vyjde `NA`**.
> - Pozor na `!(x %in% LETTERS)`: výraz `NA %in% LETTERS` vrátí `FALSE`, takže jeho negace `!FALSE` je `TRUE` -> bez `!is.na(x)` by ti prošly i `NA` hodnoty!

#### `slice()` – výběr řádků podle pozice nebo hodnoty
```r
planes %>% slice(1L:5L)               # řádky 1 až 5
planes %>% slice(c(1, 2:5))           # kombinace pozic

planes %>% slice_head(n = 5)          # prvních 5 řádků
planes %>% slice_tail(prop = 0.001)   # posledních 0,1 % řádků
planes %>% slice_min(year, n = 3)     # 3 nejstarší letadla (nejnižší year)
planes %>% slice_max(seats, n = 3)    # 3 letadla s nejvíce sedadly

# Náhodný výběr (parametry: n / prop, replace = TRUE/FALSE, weight_by)
set.seed(42)                          # pro reprodukovatelnost
planes %>% slice_sample(n = 10, replace = FALSE)
```

#### `distinct()` – unikátní řádky (lepší `unique()`)
```r
planes %>% distinct(manufacturer, type)                  # vrátí jen tyto 2 sloupce
planes %>% distinct(manufacturer, type, .keep_all = TRUE) # zachová i ostatní sloupce (první výskyt)
```

#### `arrange()` – seřazení řádků
```r
planes %>% arrange(manufacturer, engines)        # vzestupně
planes %>% arrange(desc(manufacturer), engines)  # manufacturer sestupně, engines vzestupně
```
> [!note] Chování `NA` při řazení
> `arrange()` dává hodnoty `NA` **vždy na konec** tabulky (i když použiješ `desc()`).

---

### 📋 Výběr a úprava sloupců: `select()`, `rename()`

```r
planes %>% select(tailnum, manufacturer)                 # jménem
planes %>% select(1:4, 8)                                # pozicí
planes %>% select(tailnum:manufacturer, speed)           # rozsahem
planes %>% select(-tailnum, -year, -speed)               # negativní výběr (vyhození)
planes %>% select(-tailnum:-manufacturer, -speed)        # vyhození rozsahu

planes %>% rename(company = manufacturer)                # přejmenuje (nový = starý), ostatní nechá
planes %>% relocate(speed, .before = year)               # (doplněk) přesune sloupec bez výběru
```

> [!tip] Rozdíl `select(novy = stary)` vs `rename(novy = stary)`
> Oba přejmenují sloupec, ale `select()` **zahodí všechny ostatní sloupce**, kdežto `rename()` **zachová celou tabulku**.

#### Select helpers (fungují uvnitř `select()`, `across()`, `relocate()`)

| Helper | Význam | Příklad |
| :--- | :--- | :--- |
| `starts_with()` | začíná na řetězec | `select(starts_with(c("e", "s")))` |
| `ends_with()` | končí na řetězec | `select(ends_with("s"))` |
| `contains()` | obsahuje řetězec | `select(contains("engine"))` |
| `matches()` | odpovídá regulárnímu výrazu (regex) | `select(matches("^engine"))` |
| `num_range()` | prefix + číselná řada | `select(num_range("trial_", 1:3))` |
| `everything()` | všechny (zbývající) sloupce | `select(new_col, everything())` |
| `last_col()` | poslední sloupec tabulky | `select(last_col())` |
| `all_of()` | vektor jmen – **chyba**, pokud některý chybí | `select(all_of(c("engine", "Engine")))` -> **ERROR** |
| `any_of()` | vektor jmen – **bezpečný** (vezme, co najde) | `select(any_of(c("engine", "Engine")))` -> vrátí `engine` |
| `where()` | podle datového typu / podmínky funkce | `select(where(is.integer))` |

> [!warning] `where(is.character)` – BEZ ZÁVOREK!
> Do `where()` předáváme **samotnou funkci**, ne její výsledek. Proto `where(is.character)`, nikdy `where(is.character())`.

---

### 🛠️ Tvorba a změna sloupců: `mutate()`

```r
planes %>%
  mutate(
    seats_per_engine = (seats / engines) %>% round(),
    turbo_prop_plane = engine == "Turbo-prop",
    year = log(year)   # stejné jméno = PŘEPÍŠE původní sloupec
  )
```

**Důležitá pravidla `mutate()`:**
1. **Recyklace hodnot:** Nový sloupec může dostat hodnotu délky `1` (zkopíruje se na všechny řádky, např. `x <- TRUE` nebo `rnorm(1)` nebo `mean(year, na.rm = TRUE)`) nebo vektor o přesné délce `nrow(tabulka)`. Jiná délka vyhodí chybu.
2. **Kontext výpočtu:** Defaultně počítá nad **celým sloupcem** (ne po řádcích).
3. **Pozice nového sloupce:** Přidává se na konec tabulky (lze posunout dopředu přes `select(novy, everything())` nebo argumentem `.before = 1` / `.after = ...`).

> [!tip] Podmíněné hodnoty v `mutate()` *(často u zkoušky!)*
> - Pro 2 větve: `if_else(engines > 2, "velké", "malé")`
> - Pro více větví:
> ```r
> planes %>%
>   mutate(kat = case_when(
>     seats < 50  ~ "malé",
>     seats < 150 ~ "střední",
>     .default    = "velké"
>   ))
> ```

---

### 📊 Agregace: `summarise()` (nebo `summarize()`)

Smrskne tabulku na **1 řádek** (nebo 1 řádek za každou skupinu při použití `group_by()`).

```r
planes %>%
  summarise(
    pocet       = n(),                         # (doplněk) počet řádků
    vyrobcu     = n_distinct(manufacturer),    # (doplněk) počet unikátních hodnot
    min_year    = min(year, na.rm = TRUE),
    max_year    = max(year, na.rm = TRUE),
    mean_seats  = mean(seats, na.rm = TRUE)
  )
```

> [!tip] Rychlé počítání četností: `count()` *(doplněk)*
> `planes %>% count(manufacturer, sort = TRUE)` je zkratka pro `group_by(manufacturer) %>% summarise(n = n()) %>% arrange(desc(n))`.

---

### 🔁 Operace nad více sloupci: `across()`, `if_any()`, `if_all()`

#### `across()` – uvnitř `mutate()` a `summarise()`
Syntaxe: `across(.cols, .fns)`
```r
# Změní všechny numerické sloupce na character
planes %>%
  mutate(across(where(is.numeric), as.character))

# Vlastní (anonymní) funkce
planes %>%
  mutate(across(where(is.numeric), function(x) round(x)))
# Moderní zkrácený zápis (R >= 4.1): \(x) round(x) nebo purrr styl ~ round(.x)
```

#### `if_any()` a `if_all()` – ekvivalent `across()` pro `filter()`
Vrací logický vektor (na rozdíl od `across()`).
- `if_any()`: podmínka platí **alespoň v jednom** z vybraných sloupců.
- `if_all()`: podmínka platí **ve všech** vybraných sloupcích.

```r
# Řádky, které mají NA v JAKÉMKOLIV sloupci
planes %>%
  filter(if_any(everything(), is.na))
```

---

### 📦 Práce se skupinami: `group_by()` + `ungroup()`

Rozdělí tabulku na neviditelné podskupiny. Zásadně mění chování `summarise()`, `mutate()` i `slice_*()`.

```r
# 1. group_by + summarise -> 1 řádek pro každou unikátní kombinaci skupin
planes %>%
  group_by(manufacturer, type) %>%
  summarise(
    min_seats  = min(seats, na.rm = TRUE),
    mean_seats = mean(seats, na.rm = TRUE),
    max_seats  = max(seats, na.rm = TRUE),
    .groups = "drop"   # rovnou zruší seskupení!
  )

# 2. group_by + mutate -> zachová VŠECHNY řádky, ale mean() počítá v rámci skupiny!
planes %>%
  group_by(manufacturer) %>%
  mutate(year_diff = year - mean(year, na.rm = TRUE)) %>%
  ungroup() %>%
  select(tailnum, manufacturer, year, year_diff)
```

> [!warning] Chyták: Zapomenuté `ungroup()`
> - `summarise()` odstraní vždy jen **poslední** úroveň seskupení (pokud jsi grupoval podle `a, b`, výsledek zůstane seskupený podle `a`!).
> - `group_by() + mutate()` zachová **všechny** skupiny.
> - Pokud tabulku neodgrupíš (`ungroup()` nebo `.groups = "drop"`), další `mutate()`, `filter()` nebo `slice()` budou dál potichu počítat po skupinách!

---

## 2. Práce s více tabulkami

### 🧱 Lepení tabulek: `bind_rows()`, `bind_cols()`
- `bind_rows(df1, df2)` – lepí **pod sebe** (sloupce páruje podle jména; chybějící doplní `NA`).
- `bind_cols(df1, df2)` – lepí **vedle sebe** (vyžaduje stejný počet řádků – páruje čistě podle pozice, ne podle klíče!).

---

### 🔗 Mutating joins (přidávají sloupce z `y` do `x`)

| Funkce | Které řádky zůstanou? | Co se stane bez shody? |
| :--- | :--- | :--- |
| **`left_join(x, y)`** | **Všechny z `x`** (nejčastější) | V sloupcích z `y` bude `NA` |
| **`right_join(x, y)`** | **Všechny z `y`** | V sloupcích z `x` bude `NA` |
| **`inner_join(x, y)`** | Pouze ty, které jsou **v obou** (`x ∩ y`) | Řádky bez shody vypadnou |
| **`full_join(x, y)`** | **Všechny z obou** (`x ∪ y`) | Doplní `NA` na obou stranách |

> [!note] Duplicity při joinu
> Pokud má jeden řádek v `x` více shod v `y` (vztah 1:N), řádek z `x` se v výsledku **nakopíruje** pro každou shodu. (Pro kontrolu lze použít argument `relationship = "many-to-one"` apod.)

#### Jak zadat klíč (`by`) a kolize jmen (`suffix`):
```r
# 1. Automaticky podle společných názvů sloupců
left_join(band_members, band_instruments)

# 2. Podle konkrétního sloupce + vlastní přípony při kolizi jmen
left_join(band_members, band_members, by = "name", suffix = c(".prvni", ".druhy"))

# 3. Více sloupců jako klíč
left_join(x, y, by = c("name", "band"))

# 4. Různé názvy sloupců (vlevo = jméno v x, vpravo = jméno v y)
left_join(band_members, band_instruments2, by = c("name" = "artist"))
```

#### Pokročilé podmínky spojení: `join_by()`
Umožňuje nerovnosti, nejbližší shodu a překryvy intervalů:

```r
# Nerovnost: stejné id A datum prodeje >= datum proma
left_join(sales, promos, by = join_by(id, sale_date >= promo_date))

# Rolling join: ke každému prodeji najde NEJBLIŽŠÍ předchozí promo akci
left_join(sales, promos, by = join_by(id, closest(sale_date > promo_date)))
```
- **Overlap joins** (práce s časovými/číselnými intervaly uvnitř `join_by()`): pomocné funkce `between()`, `within()`, `overlaps()`.

---

### 🧹 Filtering joins (NEPŘIDÁVAJÍ sloupce, NEDUPLIKUJÍ řádky)
Slouží pouze k **filtrování tabulky `x`** na základě toho, zda klíč existuje v tabulce `y`. Vrací vždy jen sloupce z `x`.

| Funkce | Význam | Ekvivalent v řeči |
| :--- | :--- | :--- |
| **`semi_join(x, y, by = ...)`** | Vrátí řádky z `x`, které **mají shodu** v `y` | *"Nech jen ty z `x`, co jsou i v `y`"* (bez duplikátů a bez sloupců z `y`) |
| **`anti_join(x, y, by = ...)`** | Vrátí řádky z `x`, které **NEMAJÍ shodu** v `y` | *"Nech jen ty z `x`, co v `y` chybí"* (super na hledání chyb/sirotků!) |

```r
# Kdo z kapely nemá v tabulce band_instruments přiřazený nástroj?
anti_join(band_members, band_instruments, by = "name")
```

---

### 🔄 Spojení seznamu mnoha tabulek najednou: `reduce()` (`purrr`)
`reduce(.x, .f, ...)` vezme seznam `.x`, aplikuje `.f` na první dva prvky, výsledek spojí se třetím prvkem atd.

```r
list(band_members, band_members, band_members, band_instruments) %>%
  reduce(., left_join, by = "name")
```
*(Tečka `.` reprezentuje vstup předaný operátorem `%>%`.)*

---

## 🎯 Rychlý kontrolní checklist před odevzdáním zkoušky

1. **Chybějící hodnoty (`NA`)**: Dal jsem `na.rm = TRUE` do `mean()`, `min()`, `max()`, `sum()`? Použil jsem `is.na()` místo `== NA`?
2. **Závorky v `where()`**: Napsal jsem `where(is.numeric)` bez `()`?
3. **Skupiny**: Použil jsem po `group_by()` funkci `ungroup()` (nebo `.groups = "drop"`), pokud s tabulkou dál pracuji?
4. **Join klíče**: Je v `by = c("sloupec_x" = "sloupec_y")` pořadí správně (vlevo `x`, vpravo `y`)? Nechtěl jsem místo `inner_join()` použít `semi_join()`, abych neduplikoval řádky?
5. **Přejmenování**: U `rename(nove = stare)` a `select(nove = stare)` je **nové jméno vždy vlevo**.
```