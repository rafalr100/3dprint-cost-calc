# FilamentCost — kalkulator wyceny druku 3D

Lekkie, samodzielne narzędzie webowe do wyceny wydruków FDM. Cały kod to jeden plik `index.html` — bez backendu, bez frameworków, bez kroku budowania. Otwierasz w przeglądarce i działa; hostujesz na GitHub Pages jednym kliknięciem.

> Wpisujesz parametry druku, wybierasz drukarkę z listy, a narzędzie na żywo rozkłada cenę na czynniki pierwsze: materiał, prąd, amortyzację sprzętu, maintenance, robociznę i marżę — w PLN lub EUR po aktualnym kursie ECB.

## Demo na żywo

**→ [rafalr100.github.io/filamentcost](https://rafalr100.github.io/filamentcost/)**

## Dlaczego to powstało

Większość hobbystów i małych usługodawców druku 3D wycenia „na oko" albo liczy tylko koszt filamentu, pomijając realne koszty: zużycie prądu, amortyzację drukarki czy bufor na nieudane wydruki. To narzędzie wymusza policzenie wszystkich składników i pokazuje, ile naprawdę kosztuje wydruk oraz jaki zostaje zysk po doliczeniu marży.

## Funkcje

- **Matryca ~20 drukarek** — Bambu Lab, Prusa, Creality, Anycubic z domyślną ceną i poborem mocy. Wybór z listy podstawia wartości automatycznie, ale wszystko pozostaje edytowalne. Jest też opcja „własna drukarka".
- **Pełne rozbicie kosztu** — materiał, prąd, amortyzacja, maintenance, robocizna, postprocessing, bufor awarii i marża, z interaktywnym wykresem kołowym.
- **Marża w dwóch trybach** — procent od kosztu **lub** stawka za godzinę druku (model stosowany m.in. przez Prusa Research).
- **Osobne pole robocizny** — czas własnej pracy przy projektach na zamówienie, mnożony przez stawkę godzinową.
- **Waluty PLN ↔ EUR** — kurs pobierany na żywo z [Frankfurter API](https://www.frankfurter.app/) (oficjalne dane Europejskiego Banku Centralnego), z automatycznym kursem zapasowym przy braku sieci.
- **Przelicznik VAT** — podgląd ceny netto i brutto.
- **Motyw jasny / ciemny** — przełącznik w nagłówku.
- **Kopiowanie wyceny** — sformatowane podsumowanie do schowka jednym kliknięciem.

## Jak działa wycena

Cena końcowa liczona jest według wzoru:

```
cena_brutto = (materiał + prąd + amortyzacja + maintenance + robocizna + postprocessing)
              × (1 + ryzyko_awarii)
              + marża
              × (1 + VAT)
```

Poszczególne składniki:

| Składnik | Wzór | Uwagi |
|---|---|---|
| Materiał | `(waga_g ÷ 1000) × cena_kg × (1 + zapas%)` | zapas pokrywa purge/skirt |
| Prąd | `(pobór_W ÷ 1000) × czas_h × cena_kWh` | pobór z matrycy drukarki |
| Amortyzacja | `cena_drukarki ÷ żywotność_h × czas_h` | zwrot kosztu zakupu maszyny |
| Maintenance | stała kwota per wydruk | części zużywalne |
| Robocizna | `czas_pracy_min ÷ 60 × stawka_h` | tylko projekty custom |
| Marża (%) | `koszt × marża%` | tryb procentowy |
| Marża (godz) | `czas_h × stawka_godzinowa` | tryb godzinowy |

### Dlaczego amortyzacja liczona jest przez żywotność w godzinach

Standard rynkowy dla drukarek desktopowych to **3 000–10 000 godzin** użytecznej pracy. Zamiast sztywnego „rozłóż cenę na 5 lat", narzędzie liczy amortyzację przez całkowity przebieg w godzinach (domyślnie 6 000 h — środek przedziału). To uczciwiej odzwierciedla rzeczywisty koszt na wydruk: drukarka pracująca rzadziej amortyzuje się wolniej w czasie, ale tak samo per godzina druku.

### Maintenance kontra amortyzacja

To dwie różne pozycje. Amortyzacja zwraca koszt **zakupu** maszyny. Maintenance pokrywa **bieżące zużywalne części** (dysze, paski, smar, PTFE), których amortyzacja nie obejmuje. Domyślne 0,50 zł/wydruk to przybliżenie rocznego budżetu na części (100–200 zł) rozłożonego na typową liczbę wydruków.

## Założenia domyślne

| Parametr | Wartość | Źródło / uzasadnienie |
|---|---|---|
| Żywotność drukarki | 6 000 h | środek przedziału 3 000–10 000 h |
| Maintenance | 0,50 zł / wydruk | przybliżenie kosztu części zużywalnych |
| Cena prądu | 1,00 zł/kWh | orientacyjna stawka dla gospodarstw w PL |
| VAT | 23% | stawka podstawowa w PL |
| Ryzyko awarii | 0% | wyłączone domyślnie, do włączenia ręcznie |
| Zapas filamentu | 5% | purge / skirt / brim |

> **Uwaga:** domyślne ceny i pobór mocy drukarek są **orientacyjne** — ceny rynkowe zmieniają się i różnią między sklepami. Wszystkie wartości są edytowalne; zweryfikuj je pod swój sprzęt i rynek.

## Uruchomienie lokalne

Nie wymaga niczego poza przeglądarką:

```bash
git clone https://github.com/rafalr100/filamentcost.git
cd filamentcost
# otwórz index.html w przeglądarce, albo:
python3 -m http.server 8000   # następnie http://localhost:8000
```


## Konfiguracja matrycy drukarek

Lista drukarek to tablica `PRINTERS` na początku bloku `<script>`. Każdy wpis:

```js
{g:"Bambu Lab", n:"P1S", price:2899, w:120}
//  grupa        model    cena PLN   pobór mocy W
```

Dodanie modelu = dopisanie obiektu do tablicy. `price` zawsze w PLN (kanoniczna waluta; EUR jest przeliczane w locie).

## Stack technologiczny

- Czysty HTML + CSS + JavaScript (vanilla, zero zależności runtime).
- Wykres kołowy rysowany ręcznie w SVG (`stroke-dasharray`), bez bibliotek.
- Fonty: Bricolage Grotesque (nagłówki), Hanken Grotesk (tekst), JetBrains Mono (liczby).
- Kurs walut: Frankfurter API (REST, bez klucza, CORS).

## Licencja

MIT — szczegóły w pliku [LICENSE](LICENSE).
