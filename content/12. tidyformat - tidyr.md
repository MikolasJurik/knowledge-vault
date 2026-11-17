
> [!info] **Tidy data** 
> 1. **Každé pozorování** je popsáno **jedním řádkem**.
> 2. **Každá proměnná** je obsažena v **jednom sloupci**.
> 3. **Každý typ pozorování** má **vlastní tabulku** *(v praxi se často doplňuje: každá hodnota má svou vlastní buňku)*.

## ⚡ Rychlá mapa funkcí `tidyr`

| Problém v datech                                                                             | Řešení (`tidyr` funkce)                               | Efekt na tvar tabulky                |
| :------------------------------------------------------------------------------------------- | :---------------------------------------------------- | :----------------------------------- |
| Hodnoty jedné proměnné jsou v **názvech sloupců** (např. sloupce `1999`, `2000`)             | `pivot_longer()`                                      | tabulka bude **delší a užší**        |
| Jedno pozorování je **roztahané přes více řádků** (sloupec `type` obsahuje názvy proměnných) | `pivot_wider()`                                       | tabulka bude **kratší a širší**      |
| **Více hodnot v jedné buňce** (např. `"745/19987071"`) -> chci více sloupců                  | `separate_wider_delim()` / `_position()` / `_regex()` | **více sloupců**, stejný počet řádků |
| **Více hodnot v jedné buňce** (např. odpovědi z dotazníku) -> chci pod sebe                  | `separate_longer_delim()` / `_position()`             | **více řádků**, stejný počet sloupců |
| **Jedna hodnota rozdělená do více sloupců** (`century` + `year`)                             | `unite()`                                             | **méně sloupců**                     |
| Řádek v tabulce **úplně chybí** (implicitní `NA`)                                            | `complete()`                                          | doplní chybějící kombinace jako `NA` |
| Chci **vyhodit** řádky s `NA`                                                                | `drop_na()`                                           | méně řádků                           |
| Chci **nahradit** explicitní `NA` pevnou hodnotou / předchozí hodnotou                       | `replace_na()` / `fill()`                             | stejný tvar, bez `NA`                |
| Chci vytvořit tabulku se **všemi kombinacemi** hodnot                                        | `crossing()`                                          | kartézský součin                     |

---

## 1. Pivoting: `pivot_longer()` a `pivot_wider()`

### 📏 `pivot_longer()` – z mnoha sloupců do dvojice (jméno + hodnota)
Použiješ, když **názvy sloupců jsou ve skutečnosti hodnoty proměnné** (např. roky `1999`, `2000` v `table4a`).

```r
# Základní použití (vybereme vše kromě country)
table4a %>%
  pivot_longer(
    cols = -country,          # které sloupce složit
    names_to = "year",        # název nového sloupce pro původní jména sloupců
    values_to = "cases"       # název nového sloupce pro hodnoty
  )
```

**Způsoby výběru sloupců (`cols`) v `pivot_longer()`:**
```r
pivot_longer(-c(country, id))       # negativní výběr
pivot_longer(c(`1999`, `2000`))     # pozitivní výběr (číselné názvy do zpětných uvozovek ``!)
pivot_longer(`1999`:`2000`)         # rozsah
pivot_longer(matches("\\d{4}"))     # select helper (4 číslice)
```

> [!tip] Užitečné parametry `pivot_longer()`
> - `values_drop_na = TRUE` *(doplněk)* – rovnou zahodí řádky, kde vzniklo `NA`.
> - `names_prefix = "rok_"` *(doplněk)* – odřízne prefix z názvů sloupců (např. `"rok_1999"` -> `"1999"`).

---

### ↔️ `pivot_wider()` – z dvojice sloupců do mnoha sloupců
Použiješ, když **jeden sloupec obsahuje názvy více různých proměnných** (např. sloupec `type` obsahuje `"cases"` a `"population"` v `table2`).

```r
table2 %>%
  pivot_wider(
    id_cols = c(country, year), # co identifikuje 1 řádek (NULL = automaticky vše ostatní)
    names_from = type,          # odkud vzít názvy nových sloupců
    values_from = count         # odkud vzít hodnoty
  )
```

**Vícenásobné názvy sloupců (`names_from` délky > 1):**
```r
# Vytvoří sloupce: cases_1999, population_1999, cases_2000, population_2000
table2 %>%
  pivot_wider(
    names_from = c(type, year),
    values_from = count,
    names_sep = "_"             # oddělovač při spojování jmen (default "_")
  )
```

> [!warning] Zkouškové chytáky u `pivot_wider()`
> 1. **Chybějící kombinace (`values_fill`):** Pokud nějaká kombinace v datech chybí, vznikne `NA`. Přidej `values_fill = 0` (nebo `list(sloupec = 0)`), pokud chceš nuly.
> 2. **Duplicitní klíče -> List-columns (`values_fn`):** Pokud kombinace `id_cols` + `names_from` není unikátní (např. 2 měření pro stejný stát a rok), `pivot_wider()` vyhodí varování a vytvoří sloupce typu `<list>`. Řešením je např. `values_fn = mean` nebo `values_fn = sum` *(doplněk)*.

---

### 🔄 Rozpad složených jmen sloupců zpět (`names_to` > 1 + konverze typů)
Pokud máš sloupce typu `cases_1999`, můžeš je v `pivot_longer()` **rovnou rozdělit do 2 sloupců** a přetypovat:

```r
table2_wide %>%
  pivot_longer(
    cols = -country,
    names_to = c("type", "year"),            # 2 cílové sloupce!
    names_sep = "_",                         # podle čeho rozdělit (pro složitější: names_pattern)
    names_transform = list(year = as.integer), # převede text "1999" na číslo 1999
    names_ptypes = list(type = character()),   # vynutí konkrétní datový typ (pojistka)
    values_to = "count"
  )
```

---

## 2. Rozdělování a spojování buněk

### ✂️ `separate_wider_*()` – z 1 sloupce do více sloupců
Když je v jedné buňce slepeno více proměnných (např. `rate = "745/19987071"` v `table3`).

| Funkce | Podle čeho dělí | Klíčový parametr |
| :--- | :--- | :--- |
| `separate_wider_delim()` | podle oddělovače | `delim = "/"` + `names = c(...)` |
| `separate_wider_position()` | podle pevného počtu znaků | `widths = c(a = 2, b = 4)` |
| `separate_wider_regex()` | podle regulárního výrazu | `patterns = c(...)` |

```r
# 1. Podle oddělovače
table3 %>%
  separate_wider_delim(
    cols = rate,                 # název sloupce bez uvozovek
    delim = "/",
    names = c("cases", "population"),
    too_few = "error",           # co dělat, když je kousků málo ("error", "debug", "align_start", "align_end")
    too_many = "error",          # co dělat, když je jich moc ("error", "debug", "drop", "merge")
    cols_remove = TRUE           # TRUE = smaže původní sloupec rate
  )

# 2. Podle regulárního výrazu (pojmenované části = nové sloupce, NEPOJMENOVANÉ se zahodí!)
df <- tibble(osoba = c("Jan_25_M", "Eva_30_F"))
df %>%
  separate_wider_regex(
    osoba,
    patterns = c(
      jmeno = "[A-Za-z]+",
      "_",                       # nepojmenovaný oddělovač -> zahodí se
      vek = "[0-9]+",
      "_",
      pohlavi = "[MF]"
    )
  )
```
> [!note] Datový typ po `separate_*()`
> Výsledné sloupce jsou vždy typu `<chr>` (text). Pokud z nich chceš čísla, použij následně `mutate(across(..., as.numeric))` nebo `readr::parse_number()`.

---

### ⬇️ `separate_longer_*()` – z 1 buňky do více řádků
Navýší **počet řádků**, počet sloupců zůstane stejný. Používá se typicky u dat z dotazníků (např. Google Forms, kde je v jedné buňce `"A, B, C"`).

```r
table3 %>%
  separate_longer_delim(rate, delim = "/")
# Analogicky existuje i separate_longer_position()
```

---

### 🔗 `unite()` – z více sloupců do jednoho
Opak `separate_wider_delim()`. Výchozí oddělovač je `"_"`.

```r
# Spojí century ("19") a year ("99") do sloupce year ("1999")
table5 %>%
  unite(
    col = year,          # jméno nového sloupce (bez uvozovek)
    century, year,       # které sloupce spojit (funguje stejně jako dplyr::select)
    sep = "",            # výchozí je "_"
    remove = TRUE        # smaže původní sloupce
  )
```

---

## 3. Implicitní a explicitní chybějící hodnoty (`NA`)

> [!info] Zásadní rozdíl
> - **Explicitní `NA`:** hodnota je v tabulce fyzicky zapsaná jako `NA` (viditelná okem i přes `is.na()`).
> - **Implicitní `NA`:** celý řádek v tabulce **chybí** (např. v tabulce pro Brazílii rok 1999 vůbec není). Test `is.na()` ho nenajde!

### 🛠️ Nástroje pro práci s `NA`

#### 1. `complete()` – udělá z implicitních `NA` explicitní
Vytvoří všechny kombinace zadaných sloupců a chybějící místa vyplní `NA` (nebo hodnotou z `fill`).
```r
# Doplní chybějící kombinace (country x year) jako explicitní NA
table1_impl %>% complete(country, year)

# Místo NA doplní pro sloupec cases nulu (population zůstane NA)
table1_impl %>% complete(country, year, fill = list(cases = 0))
```

#### 2. `drop_na()` – smaže řádky s explicitním `NA`
```r
table1_expl %>% drop_na()             # zahodí řádek, pokud je NA v JAKÉMKOLIV sloupci
table1_expl %>% drop_na(population)   # zahodí řádek jen tehdy, když je NA v population
```

#### 3. `replace_na()` – nahradí explicitní `NA` pevnou hodnotou
```r
table1_expl %>%
  replace_na(replace = list(cases = 0, population = 1000000))
```

#### 4. `fill()` – nahradí explicitní `NA` sousední hodnotou (LOCF)
Používá se, když v tabulce chybí opakující se hodnota (typicky z Excelu se sloučenými buňkami). Pracuje **jen s explicitními `NA`**!
```r
# Defaultně tahá hodnotu SHORA dolů (.direction = "down")
# Další možnosti: "up", "downup", "updown"
table1_expl %>% fill(cases, .direction = "down")
```

> [!warning] Kritický chyták u `fill()`!
> Podívej se na výsledek v poznámkách:
> - V 1. řádku (Afghánistán 1999) zůstalo `NA` (nad ním nic není).
> - Do Brazílie 1999 se ale zkopírovala hodnota **z Afghánistánu 2000**!
> - **Řešení:** Pokud nechceš, aby hodnoty přetékaly mezi státy/skupinami, použij **`group_by(country) %>% fill(cases) %>% ungroup()`**!

---

## 4. Vlastní mřížky a vnořené tabulky

### 🕸️ `crossing()` – generování všech kombinací
Vytvoří `tibble` obsahující kartézský součin (každou unikátní kombinaci vstupních vektorů, seřazenou a bez duplicit). Skvělé pro přípravu kostry panelových dat (např. pak na ni napojit data přes `left_join()`).

```r
crossing(> - Nikdy nepiš ￼￼x == NA￼￼ (vrátí vždy ￼￼NA￼￼)! Používej **`is.na(x)`** nebo ￼￼￼￼!is.na(x)￼￼￼￼.
  country = c("Czech Republic", "Slovakia", "Poland", "Hungary"),
  year = 2000:2001
)
# Výsledek: 8 řádků (4 státy × 2 roky)
```

### 🪆 `nest()` a `unnest()` – vnořené tabulky *(doplněk k přehledu)*
Umožňují uložit celé podtabulky do jedné buňky (vznikne *list-column* `data`, což je super např. pro fitování lineárního modelu pro každý stát zvlášť přes `purrr::map()`).

```r
# Vnoří všechny sloupce kromě country do list-sloupce `data`
nested <- table1 %>% nest(data = -country)

# Rozbalí vnořenou tabulku zpět na klasické řádky a sloupce
nested %>% unnest(cols = data)
```

---

## 🎯 Rychlý kontrolní checklist před odevzdáním zkoušky

1. **Rozdíl `longer` vs `wider`:** Přibývají řádky (`longer`) -> `names_to`, `values_to`. Přibývají sloupce (`wider`) -> `names_from`, `values_from`.
2. **Číselné názvy sloupců:** Sloupce jako `1999` musí být v kódu ve zpětných uvozovkách `` `1999` `` (nebo v uvozovkách `"1999"`).
3. **`separate_wider_regex()`:** Každý prvek vektoru `patterns`, který chceš zachovat jako sloupec, **musí mít jméno** (`vek = "[0-9]+"`). Nepojmenované řetězce (jako `"_"`) se vyhodí.
4. **Než použiješ `fill()`:** Máš v datech skutečně *explicitní* `NA` (případně po `complete()`)? A nezapomněl jsi na `group_by()`, aby hodnota nepřetekla z jedné entity do druhé?
5. **Přetypování po rozdělení:** `separate_*()` a rozpad názvů v `pivot_longer()` nechávají text (`<chr>`). Pokud s nimi pak chceš počítat, převeď je (`names_transform` nebo `as.numeric()`).
```