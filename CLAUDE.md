# 3dprint-cost-calc

Kalkulator wyceny druku 3D — aplikacja webowa (HTML/JS/CSS, zero dependencies).
GitHub: https://github.com/rafalr100/3dprint-cost-calc

## Cel projektu

Szybka wycena kosztu wydruku na Prusa MK3S (lub innej drukarce FDM).
Wpisujesz dane z slicera (gramy filamentu, czas druku) i dostajesz koszt produkcji
oraz cenę z marżą dla klientów. Działa offline, bez żadnych zewnętrznych zależności.

## Stack techniczny

- Czysty HTML/JS/CSS — żadnych frameworków, żadnego build stepu
- Jeden plik `index.html` — cała aplikacja
- Motyw jasny/ciemny (data-theme), responsywny layout
- Font: Bricolage Grotesque + Hanken Grotesk + JetBrains Mono (Google Fonts)
- Deployment: statyczny hosting (GitHub Pages lub dowolny CDN)

## Jak używać / rozwijać

```bash
# Podgląd lokalny — otwórz bezpośrednio
open index.html

# Po zmianach
git add . && git commit -m "..." && git push
```

## Parametry kalkulatora

- Zużycie filamentu [g] + typ (PLA, PETG, ABS, ASA, TPU…)
- Cena filamentu [PLN/kg]
- Czas druku [h] + moc drukarki [W]
- Cena prądu [PLN/kWh]
- Marża [%] — opcjonalna, dla wycen komercyjnych

## Powiązane

- Drukarka: Prusa MK3S
- Filament: Fiberlogy PETG, Prusament PLA
- Slicery: PrusaSlicer (podaje zużycie [g] i czas)
