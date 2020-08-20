# The worm
Jedná se o počítačovou hru, která sama o sobě nemá herní prostředí.
Hra je multi platformní a odehrává se přímo ve file systému počítače na kterém je spuštěna.

**Hlavní engine** hry je process, který vytvoří soubor. Engine na základě nějakých pravidel posouvá tento soubor zkrze souborý systém (skutečně na disku)
Uživatel má k dispozici všechny prostředky svého shellu/klikátka na to, aby tento soubor našel, zablokoval a zlikvidoval.
Engine zná souborový systém, zajišťuje určitou bezpečnost, zpracovává činnosti a časování a načítá jednotlivé moduly.

Modul je přiložený .csx skript, který lze načíst v průběhu hry a který může definovat
- druh útočícího souboru (jak se chová, jak se posouvá, jak jej lze zneškodnit)
- virtuální změny file systému (označení složky či soubory tagem, který specifikuje nějakou ochranu, nebo anopak návnadu atd.)
- možné příkazy, které lze volat (rozšíření shellu o příkazy, které volají načtené knihovny hry)

Proč něco takového vymýšlím?
- Chci hodně pracovat s .NET Core pro všechny platofrmy
- Chci napsat nějakou hru, která by byla v shellu a přímo přibližovalla hráče směrem k příkazové řádce
- Chci napsat výukovou hru pro práci s příkazovou řádkou
- Chci napsat hru která potrestá hráče za špatně vedený file systém

## Stručný popis

## Symbolický rámec
Engine vytváří soubory. Tento soubor reprezentuje útočící "virus"

## Průměh jedné hry
