# Stringy a regulární výrazy v R (`stringr`)

> [!summary] Základní princip
> `stringr` poskytuje vektorové funkce pro práci s textem. Většina funkcí pro hledání vzorů interpretuje `pattern` jako **regulární výraz (regex)**, nikoli jako doslovný text. Pro doslovné hledání použij `fixed()`.

```r
library(stringr)
library(glue)   # interpolace textu ve stylu f-stringů
```

## 1. Základy řetězců v R

### Speciální znaky a escapování

V řetězcích R se speciální znaky zapisují pomocí `\`. Protože `\` je zároveň speciální znak v regexu, musí se v R často zdvojovat.

| Zápis v R | Význam |
|---|---|
| `"\n"` | nový řádek |
| `"\t"` | tabulátor |
| `"\""` | uvozovka uvnitř řetězce |
| `"\\"` | doslovné zpětné lomítko |
| `"\u03A9"` | Unicode znak Ω |
| `"\\d"` | regex `\d` = číslice |
| `"\\."` | regex `\.` = doslovná tečka |
| `"\\\\"` | regex `\\` = doslovné zpětné lomítko |

**Pozor:** tečka `.` v regexu znamená libovolný znak; pro skutečnou tečku použij `\\.` nebo `fixed(".")`.

### Výpis a porovnávání

| Funkce | Účel |
|---|---|
| `print(x)` | vypíše objekt včetně uvozovek a escapování |
| `cat(x)` | vypíše obsah textu bez uvozovek; vhodné pro formátovaný výstup |
| `noquote(x)` | tisk bez uvozovek |
| `format(x, ...)` | formátování hodnot před výpisem |
| `toString(x)` | převod na jeden řetězec |
| `sprintf(fmt, ...)` | formátování ve stylu jazyka C |
| `str_equal(x, y)` | porovnání řetězců; `ignore_case = TRUE` ignoruje velikost písmen |

```r
str_equal("abc", "ABC", ignore_case = TRUE)
# TRUE
```

### Délka řetězce vs. délka vektoru

```r
x <- c("abc", "hello")

length(x)       # počet prvků vektoru = 2
str_length(x)   # délka každého řetězce = c(3, 5)

length("")             # 1: jeden prázdný řetězec
length(character(0))   # 0: prázdný vektor řetězců
length(character(5))   # 5: pět prázdných řetězců
```

- `length()` počítá prvky vektoru.
- `str_length()` počítá znaky v jednotlivých řetězcích.
- `letters` a `LETTERS` jsou vestavěné vektory malých a velkých písmen anglické abecedy.

## 2. Vytváření a spojování řetězců

### `str_c()` – spojování po prvcích

```r
str_c("Ahoj", "světe", sep = " ")
# "Ahoj světe"

str_c(c("a", "b"), 1:2)
# "a1" "b2"
```

- `sep` určuje oddělovač mezi spojovanými argumenty (výchozí `""`).
- Argumenty musí mít kompatibilní délky: stejnou délku nebo délku 1.
- `collapse` spojí výsledný vektor do **jednoho** řetězce.
- `NULL` se při spojování ignoruje; `character(0)` může způsobit výsledek délky 0.

```r
str_c(c("a", "b", "c"), collapse = " - ")
# "a - b - c"
```

### `str_flatten()` – spojení vektoru do textu

```r
x <- c("cukr", "káva", "limonáda")

str_flatten(x, collapse = ", ", last = " a ")
# "cukr, káva a limonáda"
```

`last` určuje oddělovač před posledním prvkem.

### `glue()` – interpolace proměnných

Obdoba f-stringů v Pythonu: výrazy uvnitř `{}` se vyhodnotí.

```r
name <- "Petr"
age <- 25

glue("Jmenuji se {name} a je mi {age} let.")
# Jmenuji se Petr a je mi 25 let.
```

```r
person <- list(name = "Joe", age = 40)

glue("Jmenuji se {person$name} a příští rok mi bude {person$age + 1} let.")
```

- `{{` a `}}` vypíší doslovné složené závorky.
- `glue()` odstraňuje vnější bílé znaky a upravuje odsazení víceřádkového textu.
- `str_glue_data()` umožňuje použít sloupce datového rámce nebo prvky seznamu přímo:

```r
person |>
  str_glue_data("Jmenuji se {name} a je mi {age} let.")
```

## 3. Čištění a formátování textu

| Funkce | Účel / důležité argumenty |
|---|---|
| `str_dup(x, times)` | opakuje každý řetězec zadaný početkrát; argumenty se recyklují |
| `str_unique(x, ignore_case = TRUE)` | odstraní duplicity; lze ignorovat velikost písmen |
| `str_trim(x, side = "both")` | odstraní bílé znaky zleva, zprava nebo z obou stran |
| `str_squish(x)` | jako `str_trim()`, navíc zredukuje vnitřní opakované bílé znaky na jednu mezeru |
| `str_pad(x, width, side, pad)` | doplní znaky na minimální šířku |
| `str_trunc(x, width, side, ellipsis)` | zkrátí text a přidá výpustku |
| `str_wrap(x, width, indent, exdent)` | zalomí text do řádků dané šířky |
| `str_to_upper(x)` | převede na velká písmena |
| `str_to_lower(x)` | převede na malá písmena |
| `str_replace_na(x, replacement)` | nahradí hodnoty `NA` textem |

```r
str_trim("  ahoj  ")            # "ahoj"
str_squish("  ahoj   světe ")   # "ahoj světe"

str_pad("Ahoj", 8, side = "left", pad = "-")
# "----Ahoj"

str_trunc("Příliš dlouhý text", 12)
# zkrácený text s "..."

str_wrap("Delší text, který chceme zalomit do více řádků.", width = 20)
```

### Formátování čísel pomocí `format()`

```r
x <- 123456.3

format(
  x,
  big.mark = ".",
  decimal.mark = ",",
  nsmall = 2
)
# "123.456,30"
```

Důležité argumenty:
- `big.mark` – oddělovač tisíců.
- `decimal.mark` – desetinný oddělovač.
- `nsmall` – minimální počet desetinných míst.
- `scientific` – použití vědecké notace.
- `width`, `justify` – minimální šířka a zarovnání.

## 4. Řazení a pořadí řetězců

| Funkce | Vrací |
|---|---|
| `str_sort(x)` | seřazený vektor řetězců |
| `str_order(x)` | indexy, které určují pořadí po seřazení |
| `str_rank(x)` | pořadí (rank) každého prvku |

Důležité argumenty:
- `locale = "cs"` – řazení podle českých jazykových pravidel.
- `decreasing = TRUE` – sestupné pořadí.
- `na_last = TRUE` – `NA` na konec; `FALSE` na začátek; `NA` je vynechá.
- `numeric = TRUE` – číslice v textu řadí číselně, např. `"x2"` před `"x10"`.

```r
x <- c("cukr", "čaj", "čaj", "káva", "limonáda", "rum")

str_sort(x, locale = "cs")
str_order(x, locale = "cs")  # pořadí indexů
str_rank(x, locale = "cs")   # shodné hodnoty mají stejný rank
```

`str_order()` vs. `str_rank()`:
- `str_order()` odpovídá spíše `order()` – vrací pořadí indexů.
- `str_rank()` vrací rank jednotlivých pozorování; shodné hodnoty dostanou stejný rank (např. 1, 2, 2, 4).

## 5. Výběr a úprava části řetězce

### `str_sub()` – indexování znaků

```r
x <- "Sol 6: Katastrofa na Marsu."

str_sub(x, start = 8, end = -1)
# "Katastrofa na Marsu."

str_sub(x, end = 5)
# "Sol 6"

str_sub(x, 22, 26) <- "rudé planetě"  # nahrazení části řetězce
```

- Indexování začíná od 1.
- `start` a `end` jsou **včetně** krajních pozic.
- Záporné indexy se počítají od konce (`-1` = poslední znak).
- Funkce je vektorová a pozice se mohou recyklovat.
- Lze použít i pro nahrazení části řetězce.

### `word()` – výběr slov

```r
word("Jednou budem možná dál!", start = 2, end = 3)
# "budem možná"

word("Jednou budem možná dál!", -1)
# "dál!"
```

`sep` určuje regex oddělující slova; záporné indexy se počítají odzadu.

## 6. Regulární výrazy (regex)

Regulární výraz popisuje **vzor**, který se má v textu najít. Ve `stringr` se regex používá standardně, pokud není pattern zabalen jinou funkcí, např. `fixed()`.

### Základní speciální znaky

| Zápis | Význam |
|---|---|
| `.` | libovolný znak kromě nového řádku |
| `\\d` | číslice |
| `\\D` | znak, který není číslice |
| `\\s` | bílý znak (mezera, tabulátor, nový řádek…) |
| `\\S` | znak, který není bílý |
| `\\w` | znak slova (písmeno, číslice, některé další znaky) |
| `\\W` | znak, který není znakem slova |
| `\\b` | hranice slova |
| `^` | začátek řetězce |
| `$` | konec řetězce |
| `\\A`, `\\z` | absolutní začátek a konec vstupu |

**Důležité:** regex engine používaný `stringr` podporuje Unicode. `\\w` tedy není omezené pouze na anglická ASCII písmena. Pro explicitní výběr Unicode písmen lze použít např. `\\p{L}`.

### Znakové třídy `[]`

| Vzor | Význam |
|---|---|
| `[abc]` | jeden znak a, b nebo c |
| `[A-Z]` | jedno velké písmeno A až Z |
| `[a-z]` | jedno malé písmeno a až z |
| `[0-9]` | jedna číslice |
| `[^abc]` | jeden znak kromě a, b, c |
| `[[:alpha:]]` | písmeno |
| `[[:digit:]]` | číslice |
| `[[:alnum:]]` | písmeno nebo číslice |
| `[[:space:]]` | bílý znak |

Pro vlastnosti Unicode lze použít např. `\\p{L}` (písmena) a `\\p{N}` (čísla).

### Kvantifikátory – počet opakování

| Zápis | Význam |
|---|---|
| `*` | 0 nebo více výskytů |
| `+` | 1 nebo více výskytů |
| `?` | 0 nebo 1 výskyt |
| `{n}` | přesně n výskytů |
| `{n,}` | alespoň n výskytů |
| `{n,m}` | od n do m výskytů |

Kvantifikátory jsou standardně **hladové (greedy)** – snaží se najít co nejdelší odpovídající část. Přidáním `?` se stanou línými (lazy):

```r
x <- '"První věta." "Druhá věta."'

str_extract_all(x, '".*"')    # jeden dlouhý match
str_extract_all(x, '".*?"')   # dva samostatné match
```

### Skupiny, alternativy a priorita

| Zápis | Význam |
|---|---|
| `(abc)` | skupina; zachytí odpovídající text |
| `\\1`, `\\2` | odkaz na první, druhou zachytávací skupinu |
| `(?:abc)` | skupina bez zachytávání |
| `abc|def` | abc nebo def |
| `(abc)*` | opakování celé skupiny |
| `abc*` | opakování pouze posledního znaku c |

Kvantifikátor se vztahuje na bezprostředně předcházející znak nebo skupinu. Závorky proto mění rozsah jeho působnosti.

### Lookahead / lookbehind

Umožňují ověřit okolní text, aniž by se tato část musela zahrnout do výsledku.

| Zápis | Význam |
|---|---|
| `(?=abc)` | za aktuální pozicí musí následovat `abc` (positive lookahead) |
| `(?!abc)` | za aktuální pozicí nesmí následovat `abc` (negative lookahead) |
| `(?<=abc)` | před aktuální pozicí musí být `abc` (positive lookbehind) |
| `(?<!abc)` | před aktuální pozicí nesmí být `abc` (negative lookbehind) |

```r
x <- c("100 Kč", "200", "300 Kč")

str_extract(x, "\\d+( Kč)")   # zahrne i " Kč"
str_extract(x, "\\d+(?= Kč)") # vrátí pouze číslo před " Kč"
```

## 7. Ovládání režimu hledání: `regex()`, `fixed()`, `coll()`, `boundary()`

| Funkce | Kdy ji použít |
|---|---|
| `regex(pattern, ...)` | běžný regex s nastavitelnými parametry |
| `fixed(pattern)` | doslovné hledání znaků, bez interpretace regexových metaznaků |
| `coll(pattern, ...)` | porovnávání textu podle jazykových pravidel (locale) |
| `boundary(type)` | hledání hranic znaků, slov, vět nebo řádků |

Časté parametry `regex()`:
- `ignore_case = TRUE` – ignoruje velikost písmen.
- `multiline = TRUE` – `^` a `$` označují začátek a konec **každého řádku**, ne pouze celého vstupu.
- `dotall = TRUE` – tečka `.` může odpovídat i novému řádku.
- `comments = TRUE` – dovoluje přehledněji komentovat a formátovat regex.

```r
str_detect("Ahoj", regex("ahoj", ignore_case = TRUE))
# TRUE

str_count("a.b", fixed("."))
# 1: hledá doslovnou tečku

str_split("jedna dvě tři", boundary("word"))
```

`boundary()` podporuje hranice typu `"character"`, `"word"`, `"line_break"` a `"sentence"`.

## 8. Hlavní funkce `stringr` pro práci s regexem

Většina funkcí má tvar `str_XXX(string, pattern, ...)`.

### Hledání a výběr

| Funkce | Výsledek |
|---|---|
| `str_detect(x, pattern)` | logický vektor: obsahuje řetězec vzor? |
| `str_subset(x, pattern)` | vrátí pouze řetězce, které odpovídají vzoru |
| `str_which(x, pattern)` | indexy odpovídajících prvků |
| `str_starts(x, pattern)` | test, zda řetězec začíná vzorem |
| `str_ends(x, pattern)` | test, zda řetězec končí vzorem |
| `str_count(x, pattern)` | počet výskytů vzoru v každém řetězci |

```r
x <- c("matka", "mačka", "maska", "Matka", "ma ka")

str_detect(x, "ma.ka")
str_detect(x, "ma.ka", negate = TRUE)
str_subset(x, "ma.ka")
str_which(x, "ma.ka")
str_ends(x, "ka")
str_count(c("jablko", "ananas"), "a")
```

- `str_detect()` vrací logický vektor stejné délky jako vstup.
- `negate = TRUE` obrátí výsledek.
- `str_extract()` vrací pouze **první** odpovídající část z každého řetězce.
- `str_extract_all()` vrací **všechny** odpovídající části (standardně jako seznam).
- `simplify = TRUE` u `str_extract_all()` vytvoří matici; chybějící pozice jsou doplněny `""`.

### Extrakce a pozice nálezů

| Funkce | Výsledek |
|---|---|
| `str_extract(x, pattern)` | první nalezený podřetězec |
| `str_extract_all(x, pattern)` | všechny nalezené podřetězce |
| `str_match(x, pattern)` | matice: celý match + zachytávací skupiny |
| `str_match_all(x, pattern)` | všechny matche a jejich zachytávací skupiny |
| `str_locate(x, pattern)` | začátek a konec prvního výskytu |
| `str_locate_all(x, pattern)` | pozice všech výskytů |

```r
tweet <- c("#Brno je skvělé a #MU také.", "Podcast #LSE")

r <- "#[[:alpha:]]+"

str_extract(tweet, r)       # první hashtag v každém řetězci
str_extract_all(tweet, r)   # všechny hashtagy
str_match(tweet, "#([[:alpha:]]+)") # celý match + zachycená skupina
str_locate(tweet, r)        # začátek a konec prvního výskytu
```

- U `str_match()` je první sloupec celý nalezený vzor, další sloupce odpovídají skupinám `(…)`.
- `str_extract(..., group = 1)` vrátí obsah první zachytávací skupiny namísto celého match.

### Nahrazování a odstraňování

| Funkce | Účel |
|---|---|
| `str_replace(x, pattern, replacement)` | nahradí první výskyt |
| `str_replace_all(x, pattern, replacement)` | nahradí všechny výskyty |
| `str_remove(x, pattern)` | odstraní první výskyt |
| `str_remove_all(x, pattern)` | odstraní všechny výskyty |

V náhradním textu lze použít zachycené skupiny pomocí `\\1`, `\\2` atd.:

```r
x <- "Datum: 06-01-1921"

str_replace(x, "(\\d{2})-(\\d{2})-(\\d{4})", "\\2. \\1. \\3")
# "Datum: 01. 06. 1921"
```

Odstranění části textu = nahrazení prázdným řetězcem `""`.

### Rozdělování řetězců

| Funkce | Výsledek |
|---|---|
| `str_split(x, pattern)` | seznam vektorů rozdělených částí |
| `str_split(x, pattern, simplify = TRUE)` | matice rozdělených částí |
| `str_split_fixed(x, pattern, n)` | matice s přesně `n` sloupci |
| `str_split_1(x, pattern)` | rozdělení jednoho řetězce do vektoru |
| `str_split_i(x, pattern, i)` | vrátí pouze i-tou část rozdělení |

```r
x <- c("jablka a hrušky a hrozny", "pomeranče a fíky")

str_split(x, " a ")
str_split(x, " a ", n = 2)
str_split_fixed(x, " a ", n = 3)
```

- `str_split()` standardně vrací seznam, protože jednotlivé řetězce mohou mít různý počet částí.
- Při `simplify = TRUE` se vrací matice; chybějící položky se doplní `""`.
- `n` omezuje počet rozdělení/částí; zbytek textu zůstane v poslední části.

## 9. Praktické regex vzory ze zápisků

### Telefonní čísla

```r
phone_regex <- "(\\+420)?[-\\s]*\\d{3}[-\\s]*\\d{3}[-\\s]*\\d{3}"

numbers <- c(
  "Leoš: 777 666 555 444",
  "Lída: +420 734 123 456, práce 777-666-555",
  "Leona: nevím"
)

str_extract_all(numbers, phone_regex)
```

Vzor zachytí volitelnou předvolbu `+420` a tři trojice číslic oddělené mezerami nebo pomlčkami. Jde o hledání formátu, nikoli o úplné ověření platnosti telefonního čísla.

### Data ve formátu den–měsíc–rok

```r
date_regex <- paste0(
  "\\d{1,2}\\.\\s*",
  "(\\d{1,2}\\.|leden|únor|březen|duben|červen|červenec|srpen|",
  "září|říjen|listopad|prosinec)\\s*\\d{4}"
)

dates <- c("1. 6. 2001", "1.1.2012", "1. červen 2016", "ahoj")
str_detect(dates, date_regex)
```

Regex ověřuje pouze podobu textu, ne kalendářní správnost data.

### Hashtagy

```r
hashtag_regex <- "#[[:alpha:]]+"

str_extract_all(
  c("Text s #hashtagem a #druhym", "Bez hashtagu"),
  hashtag_regex
)
```

### Nahrazení pomocí zachytávací skupiny

```r
x <- "Text s #Brno a #MU"

str_replace_all(x, "#([[:alpha:]]+)", "\\1 (#\\1)")
# "Text s Brno (#Brno) a MU (#MU)"
```

## 10. Časté chyby a rozdíly ke zkoušce

| Neplést si | Rozdíl |
|---|---|
| `length()` × `str_length()` | počet prvků vektoru × počet znaků v každém řetězci |
| `""` × `character(0)` | prázdný řetězec délky 1 × vektor délky 0 |
| `str_c(sep=)` × `str_c(collapse=)` | oddělovač mezi argumenty × spojení výsledného vektoru |
| `str_detect()` × `str_subset()` | logický vektor × vyfiltrované řetězce |
| `str_extract()` × `str_extract_all()` | první match × všechny matche |
| `str_match()` × `str_extract()` | celý match a zachycené skupiny × nalezený text |
| `str_locate()` × `str_extract()` | indexy pozice × samotný podřetězec |
| `str_replace()` × `str_replace_all()` | první výskyt × všechny výskyty |
| `str_split()` × `str_split_fixed()` | standardně seznam proměnlivých délek × matice s pevným počtem sloupců |
| `regex()` × `fixed()` | regulární výraz × doslovný text |
| `str_order()` × `str_rank()` | pořadí indexů × rank každého prvku |

> [!warning] Nejčastější regex chytáky
> 1. V R musí být regexové zpětné lomítko obvykle zdvojeno: `\\d`, `\\s`, `\\.`.
> 2. `.` znamená libovolný znak; doslovná tečka je `\\.` nebo `fixed(".")`.
> 3. `*` dovoluje i nula výskytů, `+` vyžaduje alespoň jeden.
> 4. Kvantifikátor se vztahuje na předchozí znak/skupinu: `ab*` ≠ `(ab)*`.
> 5. Regex standardně hledá vzor kdekoli v řetězci; `^` a `$` omezí hledání na začátek a konec.
> 6. `str_extract()` najde jen první výskyt v každém řetězci; pro všechny použij `str_extract_all()`.

## Rychlá pracovní šablona

```r
library(stringr)

# 1. Očištění
data <- str_squish(data)
data <- str_trim(data)

# 2. Kontrola, které řádky obsahují vzor
str_detect(data, regex("vzor", ignore_case = TRUE))

# 3. Výběr odpovídajících řetězců
str_subset(data, "vzor")

# 4. Extrakce
str_extract(data, "\\d+")
str_extract_all(data, "\\d+")

# 5. Nahrazení / odstranění
str_replace_all(data, "\\s+", " ")
str_remove_all(data, "[[:punct:]]")

# 6. Rozdělení
str_split(data, ",")
```

