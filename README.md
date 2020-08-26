# The worm

Tento projekt je vytvaren jako **Rocnikovy projekt** a **Bakalarska prace** na
[Matemeticko fyzikalni fakulte Univerzity Karlovy](https://www.mff.cuni.cz/).

This project is **Individual Software Project** and **Bachelor thesis** on
[faculty of mathematics and physics of Charles University](https://www.mff.cuni.cz/en).
> Tento dokument je ve stavu prubezneho vyvoje.


## Stručný popis
Jedná se o počítačovou hru, která sama o sobě nemá herní prostředí.
Hra je multi platformní a odehrává se přímo ve file systému počítače na kterém je spuštěna.


**Hlavní engine** hry je process, který vytvoří soubor. Engine na základě nějakých pravidel posouvá tento soubor zkrze souborý systém (skutečně na disku)
Uživatel má k dispozici všechny prostředky svého shellu/klikátka na to, aby tento soubor našel, zablokoval a zlikvidoval.
Engine zná souborový systém, zajišťuje určitou bezpečnost, zpracovává činnosti a časování a načítá jednotlivé moduly.

**Herní modul** je přiložený .csx skript, který lze načíst v průběhu hry a který může definovat
- druh útočícího souboru (jak se chová, jak se posouvá, jak jej lze zneškodnit)
- virtuální změny file systému (označení složky či soubory tagem, který specifikuje nějakou ochranu, nebo anopak návnadu atd.)
- možné příkazy, které lze volat (rozšíření shellu o příkazy, které volají načtené knihovny hry)

**Proč něco takového vymýšlím?**
- Chci hodně pracovat s .NET Core pro všechny platofrmy
- Chci napsat nějakou hru, která by byla v shellu a přímo přibližovalla hráče směrem k příkazové řádce
- Chci napsat výukovou hru pro práci s příkazovou řádkou
- Chci napsat hru která potrestá hráče za špatně vedený file systém


## Symbolický rámec
Engine vytváří soubory. Tento soubor reprezentuje útočícího "červa". 
Cíl tohoto souboru je definován modulem, který ho vytvořil. 
Může se například jednat o (virtuální) útok na původní skutečné soubory.

> - Odeslání souboru po síti, jeho přepsání, zašifrování. 
> - Analýza file systému (Worm se snaží sbírat co nejvíc dat a za ty má body. Vytvořením virtuálního fs ho lze zmást a body mu odebrat)


Cílem hráče je pak útorčníka zastavit.

## Herni API a GUI
### Engine `twgame.exe`
Po spuseni hry ma hrac k dispozici ma zjednoduseny shell, pomoci ktereho muze menit nastaveni hry.
- nacist, odstranit herni moduly pro wormy a prikazy
- nacist balicky modulu (.zip soubor s moduly definujici funkci hru)
- overit integritu modulu (kompatibilitu, aliasy a konecnost)
- zvolit vychozi slozku (koren) pro hru
- vygenerovat novou cast file systemu, slozky a soubory, ktere budou na konci hry opet smazany
- vygenerovat soubor s aliasy pro standartni shelly (bash, zsh, powershell)

> **Future feature:** Moznost stahnout oficialni moduly z nejakeho cloudu 

### Securitron `twsec.exe`
Zjednoduseny shell pro zpravu hry. 
Pracuje ve dvou rezimech, napoji se na bezici hru nebo pracuje samostatne.

Umoznuje:
- pozastavit, spustit, ukoncit hru
- zobrazit logy z predchozich her
- vymazat virtualni file systemy z predchozich her
- zobrazit/zobrazovat aktualni stav hry, pozici souboru, virtualni soubory a slozky

> Neni urcen pro pouzivani hracem, spise spravcem hry pro ucely bezpecnosti

### Console `twcon.exe`
Zjednoduseni shell pro pouziti prikazovych modulu. 
Napojuje se na bezici hru. Pokud hra nebezi, ukonci se s chybou.

**Spusteni bez parametru:**
Spusti se jednoduchy shell, ktery podporuje prikazy:
- `ls [directory]`
- `cd [directory]` | ` cd ..` | `cd .`
- `pwd` 
- `echo [text]`
- vsechny prikazy v aktualne pouzvanych modulech.

**Spusteni s parametrem** `-c [Args]` Automaticky provede prave jeden vypsany ukon a nasledne se ukoci.
Myslenka je umoznit uzivateli pouzit a liasovani pro prikazy
```
$ twcon.exe -c prot file2 file3
file2 protected
file3 protected
$ alias prot='twcon.exe -c'
$ prot file4 file150
file4 protected
file150 protected
```


## Možné moduly hry
- **File system analyzer modul** - Cílem červa je získat co nejvíce bodů tím, že "přečte" co nejvíce souborů. Za přečtení virtuálního souboru dostane bodovou penalizaci. Červ je zničen pokud dosáhne záporného množství bodů. 
- **MultyPlayer modul** - Jsou vytvořeni `N` červi typu **File system analyzer**. Cílem 1 z `N` hráčů je pak zničit jemu přiřazeného červa co nejrychleji. Má možnost bránit svému červu v postupu, ale také možnost podpořit nepřátelské červi.


## Průměh jedné hry
Uzivatel **spusti herni engine** `twgame.exe` a nastavi hru. 
> **Vyberme nasledujici moduly**:
> - Modul search worm - tento worm prochazi file system beznym zpusobem (cd d, cd ..) a "kopiruje" (vnitrne nastavi tag copied na true) si soubory
> - Modul prikazu
> 	- Lock under - uzamkne slozku pokud v ni neni worm tak, ze worm do ni nemuze vstoupit
> 	- Lock root  - uzamkne slozku tak, ze z ni worm nemuze vystoupit
> 	- Trace worm - vypise pocet prikazu (cd .., cd dir) ktere jsou potreba k dosazeni wormu
> 	- Protect    - zameti wormu intereagovat se soubory (vnitrne nastavi tag protected na true)

**Zacatek hry.** Vytvori se soubor s nahodnym jmenem. Tento soubor zacne okamzite skakat ze slozky do slozky (skok za 3 sec).
Uzivatel zacne vyhledavat pomoci prikazu `trworm`.
```
$ trworm 
N
$ cd dir
$ trworm 
N - 1 # hit
$ lockr # nzni worm nemuze vyse
$ trworm 
K
$ cd dir123
$ trworm 
K + 1 # miss
$ cd ..
$ locku dir123
```
Jakmile nalezne worm. Muze mu zacit likvidovat moznosti
``` 
$ ls
dir1 dir2 dir3 file1 file2 file3 34A597W4626556C
$ locku dir1
$ locku dir2 dir3
$ prot file1
Error. file is copied
$ prot file2 file3
```
V tuto chvili nema worm co delat, nemuze ani cist ani se pohnout a je tim znicen.
Hra tim konci.

Hra by zkoncila i v pripade ze worm oznaci vsechny dostupne soubory jako copied.

