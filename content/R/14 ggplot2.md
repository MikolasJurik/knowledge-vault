## 1. Základní princip

`ggplot2` skládá graf z **vrstev**. Každá vrstva přidává geometrii nebo statistické zobrazení; později přidané vrstvy překrývají dřívější.

```r
library(tidyverse)

ggplot(data = df, mapping = aes(x = x, y = y)) +
  geom_point() +
  geom_smooth()
```

- `ggplot()` vytvoří základ grafu (data + výchozí mapování); samotný graf může být zatím prázdný.
- `aes()` určuje **mapování proměnných na estetiky**.
- `geom_*()` určuje, **jak se data vykreslí**.
- Graf je objekt – lze jej uložit a dále skládat: `p <- ggplot(...) + geom_point(); p + geom_smooth()`.
- Vrstvy se přidávají operátorem `+` (ne `%>%`).

### Hlavní skupiny prvků

1. **Geometrie** – body, čáry, sloupce, histogramy apod.
2. **Škály (`scale_*()`)** – převod hodnot proměnných na vizuální vlastnosti, osy, popisky a legendy.
3. **Vzhled (`theme()`)** – písmo, pozadí, mřížka, umístění legendy apod.

Další důležité komponenty: `labs()` (názvy a popisky), `facet_*()` (rozdělení grafu) a `coord_*()` (souřadnicový systém).

## 2. Geometrie (`geom_*()`)

| Funkce | Použití |
|---|---|
| `geom_point()` | Bodový graf; vztah dvou proměnných |
| `geom_line()` | Spojnicový graf, typicky časová řada |
| `geom_col()` | Sloupce, jejichž výška je dána hodnotou `y` v datech |
| `geom_bar()` | Sloupce zobrazující četnosti (počítá pozorování) |
| `geom_histogram()` | Rozdělení spojité proměnné pomocí intervalů (*bins*) |
| `geom_boxplot()` | Porovnání rozdělení mezi skupinami |
| `geom_smooth()` | Odhad/proložená křivka, často s intervalem spolehlivosti |
| `geom_text()` | Textové popisky |
| `geom_tile()` | Dlaždice, typicky heatmapa |
| `geom_hex()` | Hexbin graf pro velké množství překrývajících se bodů (`hexbin`) |

**Důležitý rozdíl:**
- `geom_bar()` má standardně `stat = "count"` – počítá počet řádků pro každou kategorii; zpravidla stačí `aes(x = kategorie)`.
- `geom_col()` má `stat = "identity"` – používá již vypočtené hodnoty, např. `aes(x = kategorie, y = hodnota)`.
- `geom_histogram()` dělí spojitou proměnnou do intervalů; počet/šířku intervalů lze řídit např. `bins = 30` nebo `binwidth = 5`.

## 3. Estetiky: mapování vs. nastavení

Estetika je vizuální vlastnost objektu: `x`, `y`, `color`, `fill`, `size`, `shape`, `alpha`, `linetype`, `stroke` aj. Dostupné estetiky závisejí na geometrii.

**Mapování** = estetika se mění podle hodnot proměnné; patří dovnitř `aes()`.

**Nastavení** = pevná hodnota stejná pro všechny objekty vrstvy; patří mimo `aes()`.

```r
ggplot(df, aes(x = height, y = weight)) +
  geom_point(aes(color = gender, size = age))  # mapování

# pevná barva a průhlednost pro všechny body
ggplot(df, aes(height, weight)) +
  geom_point(color = "steelblue", alpha = 0.5, size = 2)
```

- Mapování v `ggplot(..., aes(...))` je **globální** a standardně jej dědí všechny vrstvy.
- Mapování v `geom_* (aes(...))` je **lokální** pouze pro danou vrstvu.
- Lokální mapování může globální mapování doplnit nebo přepsat.
- Vrstva může mít vlastní `data` a `mapping`; `inherit.aes = FALSE` vypne dědění globálního mapování (užitečné pro vrstvu s jinou tabulkou).
- `color`/`colour` značí barvu obrysu, čáry či bodu; `fill` značí výplň (funguje jen u geometrií, které výplň podporují).
- `alpha` = průhlednost (obvykle 0 = průhledné, 1 = neprůhledné).
- Jednu proměnnou lze namapovat na více estetik, např. `aes(color = cut, shape = cut)`.

## 4. Škály (`scale_*()`)

Škály převádějí datové hodnoty na estetiku a řídí osy, jejich popisky, rozsah, značky (*breaks*) i legendy. Pokud škálu neurčíme, `ggplot2` vybere vhodnou automaticky podle typu proměnné.

Obecná syntaxe: `scale_<estetika>_<typ>(...)`.

```r
scale_x_continuous(
  name = "Název osy",
  breaks = c(0, 1, 2),
  labels = c("málo", "středně", "hodně"),
  limits = c(0, 3),
  position = "top"
)
```

- `continuous` – spojitá proměnná; `discrete` – kategoriální/faktorová proměnná.
- Příklady: `scale_x_continuous()`, `scale_x_discrete()`, `scale_y_continuous()`, `scale_x_date()`.
- Transformace např. `scale_x_continuous(transform = "log10")` nebo zkratkou `scale_x_log10()`. Ve starších zápiscích se objevuje argument `trans`; v aktuálním ggplot2 4.x je nahrazen argumentem `transform`.
- `breaks` určuje polohy značek osy/legendy; `labels` jejich text; `limits` rozsah škály; `name` název osy/legendy.

### Barvy

- **Spojitá proměnná:** barevný přechod.
  - `scale_color_gradient(low = "blue", high = "red")`
  - `scale_fill_gradient2(low = "blue", mid = "white", high = "red", midpoint = 0)` – tři barvy kolem středu.
  - `scale_fill_distiller(palette = "YlGnBu", direction = 1)` – palety ColorBrewer.
- **Diskrétní proměnná:** odlišné barvy pro kategorie.
  - `scale_color_hue(direction = -1)` – výchozí rovnoměrně rozložené odstíny.
  - `scale_color_brewer(palette = "YlGnBu")` – předpřipravená paleta.
  - `scale_fill_manual(values = c("A" = "blue", "B" = "orange"))` – ruční přiřazení; pojmenovaný vektor zajišťuje správné přiřazení ke kategoriím.

Pozor: pro barvu bodů/čar se typicky používá `scale_color_*()`, pro výplň `scale_fill_*()`.

## 5. Grupování

`aes(group = ...)` určuje, která pozorování se mají při vykreslení/statistické transformaci považovat za jednu skupinu. Není to totéž jako `dplyr::group_by()`.

```r
ggplot(diamonds, aes(carat, price, group = cut)) +
  geom_point() +
  geom_smooth()
```

Výsledkem je samostatná proložená křivka pro každou kategorii `cut`. Mapování `color = cut` často skupiny automaticky rozliší i barevně; explicitní `group` je užitečné, když chceme skupiny odlišit, ale ne nutně barvou.

## 6. Statistické transformace (`stat_*()`)

Statistika transformuje vstupní data před vykreslením – např. počítá četnosti nebo odhaduje vyhlazenou křivku.

- `geom_smooth()` standardně používá statistiku `stat_smooth()`.
- `geom_bar()` standardně používá `stat_count()`.
- `geom_histogram()` používá `stat_bin()`.
- `geom_*()` a `stat_*()` jsou často dvě strany stejné vrstvy; obvykle se používá přehlednější `geom_*()`, ale lze nastavit jinou geometrii/statistiku.
- `stat = "identity"` znamená, že se hodnoty dále statisticky nepřepočítávají.

Příklad lineární regrese: `geom_smooth(method = "lm", se = FALSE)`.

## 7. Pozicování (`position`)

Určuje, jak se geometrické objekty umístí, zejména při překryvu nebo skupinách.

| Volba | Efekt |
|---|---|
| `"identity"` | Původní pozice; nic neupravuje |
| `"jitter"` | Přidá malou náhodnou odchylku, aby se body nepřekrývaly |
| `"dodge"` | Umístí skupiny vedle sebe |
| `"stack"` | Skládá skupiny nad sebe (výchozí např. u sloupců) |
| `"fill"` | Skládá nad sebe, ale normalizuje celkovou výšku na 1 (podíly) |
| `"jitterdodge"` | Kombinuje jitter a dodge |
| `"nudge"` | Posune geomy o zadanou vzdálenost (`nudge_x`, `nudge_y`) |

```r
ggplot(df, aes(category, fill = group)) +
  geom_bar(position = "dodge")   # skupiny vedle sebe

geom_bar(position = "fill")      # relativní podíly
geom_point(position = "jitter")  # rozptýlení překrytých bodů
```

## 8. Facetování (`facet_*()`)

Rozdělí jeden graf do více menších panelů podle kategorií proměnné.

- `facet_wrap(vars(sex))` – panely podle jedné proměnné; automaticky je zalamuje.
- `facet_wrap(vars(sex, age), nrow = 1)` – kombinace proměnných, lze nastavit `nrow`/`ncol`.
- `facet_grid(rows = vars(sex), cols = vars(age))` – mřížka panelů podle řádkové a sloupcové proměnné; ekvivalentní zápis formule je `facet_grid(sex ~ age)`.
- `facet_grid(. ~ sex)` – pouze sloupce; `facet_grid(sex ~ .)` – pouze řádky.
- `scales = "fixed"` (výchozí) – stejné osy ve všech panelech; `"free"`, `"free_x"` nebo `"free_y"` dovolí nezávislá měřítka. Volná měřítka usnadní čtení různých rozsahů, ale ztíží přímé porovnání panelů.

## 9. Souřadnicové systémy (`coord_*()`)

- `coord_cartesian()` – výchozí kartézský systém; také umožňuje přiblížit výřez grafu.
- `coord_fixed()` – pevný poměr stran; `coord_equal()` je poměr 1:1.
- `coord_flip()` – prohodí osy x a y; v současném ggplot2 je označen jako zastaralý (*superseded*), doporučuje se prohodit `x` a `y` v `aes()` (případně použít `orientation`).
- `coord_transform()` – transformace souřadnic; starší `coord_trans()` je v ggplot2 4.x deprecated. Transformace os se často řeší přímo přes `scale_*()`.
- `coord_sf()` – mapová/geografická data.

### `scale_*()` vs. `coord_cartesian()` – důležitý rozdíl

- `scale_* (limits = ...)` omezuje škálu; hodnoty mimo rozsah se standardně vyřadí/cenzurují, což může ovlivnit statistickou transformaci (např. regresní křivku nebo histogram).
- `coord_cartesian(xlim = ..., ylim = ...)` pouze **ořízne zobrazenou oblast** – data zůstávají při výpočtu statistik zachována. Použij pro skutečné „zoomování“ bez změny výpočtu.

## 10. Vzhled, popisky a legenda

`theme()` mění vzhled grafu, nikoli mapování dat.

```r
p +
  labs(title = "Název", subtitle = "Podnadpis",
       x = "Osa X", y = "Osa Y", color = "Skupina") +
  theme_classic() +
  theme(
    legend.position = "bottom",
    axis.text.x = element_text(angle = 90, vjust = 0.5)
  )
```

- Přednastavená témata: `theme_minimal()`, `theme_classic()`, `theme_bw()`, `theme_void()` aj.
- `theme()` upravuje např. `axis.text`, `axis.title`, `legend.position`, `legend.direction`, `panel.grid`, `plot.title`.
- `element_text()` – text; `element_line()` – čáry; `element_rect()` – obdélníky/pozadí; `element_blank()` – skryje prvek.
- Název legendy lze nastavit přes `labs(color = "...", fill = "...")` nebo příslušné `scale_* (name = "...")`.
- Legenda dole: `theme(legend.position = "bottom")`.

## 11. Diskretizace spojité proměnné

`cut()` převede spojitou proměnnou na kategoriální intervaly – lze ji pak zobrazit např. jako skupiny na ose nebo jako kategorie barev.

```r
ggplot(df, aes(x = cut(age, breaks = c(0, 20, 40, 60, 80)), y = value)) +
  geom_col()

ggplot(df, aes(category, year,
                fill = cut(value, breaks = c(0, 20, 40, 60, 80)))) +
  geom_tile()
```

## 12. Praktické doplňky

- `labs()` nastavuje titulky a názvy os/legend; `theme()` nastavuje jejich vzhled.
- Pro uložení grafu: `ggsave("graf.png", plot = p, width = 8, height = 5, dpi = 300)`.
- Často užitečné kombinace:
  - Bodový graf s regresním trendem: `geom_point() + geom_smooth(method = "lm")`.
  - Histogram: `ggplot(df, aes(x)) + geom_histogram(bins = 30)`.
  - Boxplot podle skupiny: `ggplot(df, aes(group, value)) + geom_boxplot()`.
  - Heatmapa: `ggplot(df, aes(x, y, fill = value)) + geom_tile()`.

## Co si u zkoušky neplést

1. **`aes()` uvnitř = mapování na proměnnou; mimo `aes()` = pevné nastavení.**
2. **`geom_bar()` počítá četnosti; `geom_col()` používá již zadané hodnoty `y`.**
3. **`group` v `aes()` řídí skupiny ve vrstvě; `group_by()` patří do dplyr a mění seskupení dat při manipulaci.**
4. **`scale_*()` mění datovou škálu a může vyřadit hodnoty; `coord_cartesian()` pouze přibližuje výřez.**
5. **`scale_*()` = mapování dat na estetiku; `theme()` = čistě vzhled grafu.**
6. **Globální `aes()` dědí vrstvy; lokální `aes()` platí jen pro konkrétní vrstvu.**
