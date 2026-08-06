---
---
# Export hovorů z OptimCall 4 do Google Sheets

Revize: 6. 8. 2026

## Co tento dokument popisuje

Do vaší tabulky Google Sheets se automaticky přenášejí záznamy o telefonních hovorech
z ústředny OptimCall 4. Každý řádek tabulky je jeden hovor, každý sloupec jeden údaj
o něm.

Tento dokument vysvětluje, **co v jednotlivých sloupcích najdete a jak tomu rozumět**.


## Anatomie hovoru

Ústředna vidí hovor jako sled navazujících událostí — kam byl hovor směrován,
kdo ho nakonec zvedl a jak dlouho trvala každá jeho část. Právě tato data
tabulka zaznamenává. Následující sekce vysvětluje klíčové koncepty, bez jejichž
znalosti je obtížné data v tabulce správně interpretovat.

### Volaný a ten, kdo hovor zvedl, nemusí být tentýž

Zákazník volá na veřejné číslo firmy. Ústředna hovor směruje na klapku, tam ho někdo
zvedne. **Volané číslo** je tedy veřejné číslo firmy, ale **hovor přijala klapka**.

```
zákazník     ──volá na──▶  228 229 501     ──ústředna spojí na──▶ 500
604 110 265                (veřejné číslo)                        (klapka)

číslo volajícího           volané číslo                           hovor přijal
```

### Rozdělení hovorů

Hovory lze kategorizovat podle několika kritérií.

Především rozlišujeme hovory **externí** a **interní**.

- **externí** — hovor mezi firmou a veřejnou telefonní sítí
- **interní** — hovor v rámci firmy, mezi klapkami. Scénář volané
  klapky však může hovor dále směrovat na jiné klapky nebo externí čísla (např.
  mobily).

U externích hovorů lze rozlišit **směr**:

- **příchozí** — hovor z veřejné telefonní sítě do firmy. Externí
  číslo volá na veřejné číslo firmy, scénář na tomto čísle hovor dále směruje
  na klapky nebo jiná externí čísla (např. mobily).
- **odchozí** — hovor z firmy do veřejné telefonní sítě - klapka volá na
  externí číslo.

U interních hovorů **směr nemá smysl** - hovor je pro jednoho účastníka
odchozí a pro druhého příchozí, takže mu nelze přiřadit jediný směr.

Pokud se tedy mluví o **odchozích** a **příchozích** hovorech, znamená to
automaticky hovory **externí** a tato skutečnost se často už neuvádí.

Hovor může skončit jako **přijatý** nebo **nepřijatý**.

- **přijatý** — jeden z příjemců hovor zvedl, proběhla konverzace mezi
  účastníky.
- **nepřijatý** — žádný z příjemců hovor nezvedl. Hovor je považován za
  **nepřijatý** i pokud OptimCall přehrával volajícímu hlášky a volající
  tedy hovor vnímal jako zvednutý.

Tyto dva základní **výsledky hovoru** jsou doplněny několika dalšími,
které pokrývají speciální případy, např. že byl hovor spojený do hlasové
schránky.

Všechny platné kombinace uvedených kritérií jsou v uživatelském rozhraní OptimCall
zakódovány do ikony ve sloupci Typ v tabulce s historií hovorů. V exportovaných
datech jsou však reprezentovány odděleně.

### Funkce klikni a volej

Místo ručního vytočení je možné **odchozí** (nebo interní) hovor zahájit **kliknutím
na číslo** v uživatelském rozhraní OptimCall nebo systému třetí strany. Když
uživatel klikne, ústředna nejprve zavolá **jemu** a teprve když zvedne, vytáčí
volaného. Tento první krok OptimCall označuje jako fázi **sestavení**.

Zatímco ručně je možné vytáčet odchozí nebo interní hovory jen z klapek, při
klikni a volej lze jako číslo volajícího zvolit i externí číslo, např. mobil.
Může tak vzniknout odchozí hovor, kde je číslo volajícího i volaného externí.

### Formáty čísel
Veřejná čísla jsou v mezinárodním formátu a rozdělená podle zvyklostí dané země
— například +420 604 110 265.

Klapky se vypisují tak, jak jsou — např. 500.

### Jak fungují jména
Ke každému číslu se OptimCall pokusí najít jméno v adresáři. Není-li číslo
v adresáři, buňka se jménem zůstane prázdná.

Jméno se dohledává ve chvíli, kdy se řádek do tabulky zapisuje. Když kontakt do
adresáře přidáte později, u starých řádků se jméno nedoplní.

### Fáze hovoru

Hovor prochází několika fázemi. Které z nich nastanou, závisí na druhu hovoru.
Fáze, která nenastala, má nulovou délku.

- **sestavení** — Nastane jen při použití funkce klikni a volej. Jde o dobu, po
  kterou OptimCall vyzvání na zařízeních **volajícího**, dokud volající hovor
  nepřijme nebo dokud nevyprší časový limit pokusu.
- **uvítání** — Nastává pouze u externích příchozích a interních hovorů. Jde
  o dobu strávenou přehráváním audio souborů s počátečními oznámeními a
  informacemi, interakcí s volajícím v rozcestníku, případně dalšími akcemi
  předtím, než začne jakýkoli pokus o spojení volajícího s příjemci.
- **vyzvánění** — Jde o dobu od zahájení směrování hovoru k příjemcům až do
  okamžiku, kdy je hovor **přijat nebo ukončen**. Tato fáze tedy začíná stavem,
  kdy hovor vyzvání na klapkách nebo externích číslech příjemců, může ale
  pokračovat tak, že se vyzvánění přeruší a OptimCall volajícímu přehraje
  oznamovací hlášku. Poté může hovor znovu vyzvánět příjemcům a tato sekvence se
  může i vícekrát opakovat dokud po posledním pokusu o vytáčení příjemců nedojde
  k přehrání závěrečných hlášek.
- **konverzace** — Zahrnuje dobu od okamžiku, kdy hovor přijme příjemce, do
  jeho **ukončení**. Tato fáze zahrnuje i stavy, kdy samotná konverzace
  neprobíhá — například přepojování nebo přidržení hovoru. Zahrnuje i pohovorové
  interakce, kdy již lokální strana zavěsila, ale vzdálená strana zůstává
  spojena, například s automatem pro hodnocení hovoru.

#### Příchozí hovor

```
├── uvítání ──┼── vyzvánění ──┼───────── konverzace ─────────┤
```
Úspěšně spojený příchozí hovor zahrnuje uvítání, vyzvánění a konverzaci.
Uvítání může chybět a hovor může rovnou začít vyzváněním. Stejně tak může
chybět vyzvánění, když je účelem pouze volajícímu přehrát hlášky,
například mimo pracovní dobu. Pokud hovor nebyl přijatý, chybí fáze
konverzace.

#### Odchozí hovor

```
├── vyzvánění ──┼───────── konverzace ─────────┤
```
Odchozí hovor nemá fázi uvítání, začíná se hned vyzváněním. Pokud jej volaný
přijme, následuje fáze konverzace.

#### Odchozí hovor přes klikni a volej

```
├── sestavení ──┼── vyzvánění ──┼───────── konverzace ─────────┤
```
Odchozí hovor zahájený přes klikni a volej přidává na začátek fázi sestavení.
Hovor může skončit již v této fázi, jinak pokračuje stejně jako běžný odchozí
hovor.

### Délka hovoru a jeho částí

Kromě celkové délky hovoru systém také měří délky jeho částí. Tyto části však
zcela nekopírují výše uvedené fáze.

Následující diagram ukazuje obecný příchozí hovor. V prvním řádku jsou jeho fáze,
ve druhém řádku potom části, jejichž délku OptimCall měří.

```
├── uvítání ──┼── vyzvánění ──┼── konverzace ─────────────────────────────┤
├── uvítání ──┼── vyzvánění ──┼── konverzace ──┼── pohovorová interakce ──┤
│                                                                         │
├─── délka ───┼──── délka ────┼──── délka ─────┼──── délka pohovorové ────┤
│   uvítání       vyzvánění       konverzace            interakce         │
│                                              
├──────────── celková délka hovoru ────────────┤
```

Stejný diagram pro obecný odchozí hovor.
```
├── sestavení ──┼── vyzvánění ──┼── konverzace ─────────────────────────────┤
├── sestavení ──┼── vyzvánění ──┼── konverzace ──┼── pohovorová interakce ──┤
│                                                                           │
├──── délka ────┼──── délka ────┼──── délka ─────┼──── délka pohovorové ────┤
│   sestavení       vyzvánění       konverzace   │        interakce         │
                │                                │
                ├───── celková délka hovoru ─────┤
```

Zatímco pro účely rozdělení hovoru na fáze se do fáze konverzace započítávají
i pohovorové interakce, pro účely měření délek je za konverzaci považována
pouze ta část, dokud jsou v hovoru oba účastníci (včetně přepojení nebo 
přidržení hovoru). Délka pohovorové interakce je měřená zvlášť.

Podobně je zvlášť měřena délka sestavení hovoru při klikni a volej.

Je to proto, aby byly délky hovorů při počítání statistik srovnatelné. Čekání, 
než obsluha zvedne své vlastní zavolání od ústředny, není součástí hovoru se 
zákazníkem — je to jen jiný způsob, jak vytočit číslo. Obdoba ručního vyťukání 
na klávesnici, které se také do celkové délky hovoru nepočítá. A automatická 
anketa přehraná po tom, co obsluha zavěsí, také není konverzací v běžném slova 
smyslu a zkreslovala by ve statistikách čas produktivní práce operátora.

#### Poznámky
Z výše uvedeného plyne, že celková délka hovoru není součtem ostatních délek.

Všechny časy jsou zaokrouhlené na celé sekundy. Z důvodu zaokrouhlování může mít
fáze, která opravdu proběhla, i nulovou délku.

Podrobnější strukturu hovoru pro detailní analýzu lze získat pomocí OptimCall 
API. Lze tak například zjistit, zda byl hovor během konverzace přepojen, nebo jiné
podrobnosti.


## Přehled sloupců

### ID

Jednoznačný identifikátor hovoru. Slouží k tomu, abyste konkrétní hovor našli v ústředně
nebo jej nahlásili na podporu.

### Začátek

Datum a čas, kdy hovor začal — v čase platném v České republice.

U příchozích hovorů jde o okamžik, kdy hovor dorazil do ústředny. U odchozích o okamžik,
kdy volající hovor zahájil.

### Typ

Zařazení hovoru podle toho, kdo jsou jeho účastníci.

- `externí` — hovor mezi firmou a veřejnou telefonní sítí
- `interní` — hovor v rámci firmy, mezi klapkami

### Směr

Směr hovoru z pohledu firmy.

- `příchozí` — hovor z veřejné telefonní sítě do firmy
- `odchozí` — hovor z firmy do veřejné telefonní sítě
- `---` — směr nemá smysl. Týká se interních hovorů, protože takový hovor je pro
  jednoho účastníka odchozí a pro druhého příchozí.

### Číslo volajícího

Kdo volal.

U odchozího hovoru z klapky je zde **číslo klapky**, ne veřejné číslo, které uvidí volaný.
To najdete ve sloupci Odchozí číslo.

### Jméno volajícího

Jméno přiřazené k číslu volajícího v adresáři systému OptimCall.

### Volané číslo

Číslo, **na které se volalo**. Pozor, není to nutně číslo, kde hovor někdo zvedl.

U příchozího hovoru je zde typicky veřejné číslo firmy a hovor OptimCall dále směruje
na klapky nebo externí čísla, kde jej příjemci mohou zvednout.

U interního hovoru je zde číslo volané klapky, která jej může zvednout, ale hovor může
být také směrovaný dále stejně jako v případě příchozího hovoru.

U odchozího hovoru se jedná o číslo protistrany.

### Jméno volaného

Jméno přiřazené k volanému číslu v adresáři systému OptimCall.

### Hovor přijal - číslo

Číslo, na kterém byl hovor skutečně zvednut. Pokud hovor nikdo nezvedl, buňka je prázdná.

U příchozího hovoru se jedná typicky o číslo klapky. Byl-li hovor směrován
na externí číslo (např. mobil) a přijat tam, je zde toto externí číslo.

U odchozího hovoru je zde volané číslo, tedy ten, komu jste se dovolali.

Pokud byl hovor přepojen, je zde poslední číslo, které hovor přijalo.

### Hovor přijal - jméno

Jméno přiřazené v adresáři systému OptimCall k číslu, které hovor přijalo.

### Odchozí číslo

Číslo zobrazené na telefonu příjemce, indikující, kdo volá (identifikace 
volajícího / CLIP / caller ID).

Používá se pouze u odchozích hovorů. U ostatních typů hovorů je buňka prázdná.

### Odchozí číslo - jméno

Jméno přiřazené v adresáři systému OptimCall k odchozímu číslu.

### Poslední fáze

Nejvzdálenější fáze, kam se hovor dostal.

- `sestavení` — Volající nezvedl hovor nebo jej odmítl, když OptimCall volal na
  zařízení **volajícího** u funkce klikni a volej.
- `uvítání` — Hovor skončil dříve, než začalo vyzvánění na klapkách či externích
   číslech příjemců.
- `vyzvánění` — Hovor zahájil vyzvánění na klapkách či externích
   číslech příjemců, ale nikdo jej nezvedl.
- `konverzace` — Hovor byl spojený s některým z příjemců.
- `hlasová schránka` — Hovor byl přesměrován do hlasové schránky.
- `blokováno` — Hovor byl zablokován, protože číslo volajícího je na seznamu
  blokovaných čísel.
- `chyba` — Hovor selhal kvůli chybě.

Podle poslední fáze se pozná, zda byl hovor **přijatý**: přijaté hovory mají poslední
fázi `konverzace`, protože jen ty se dostaly ke konverzaci s příjemcem. U všech
ostatních hodnot hovor nikdo nepřijal.

Pozor na to, že hovor s poslední fází `uvítání` volající vnímal jako zvednutý —
OptimCall mu přehrával hlášky. Přesto jde o hovor **nepřijatý**, protože se nedostal
k žádnému příjemci.

Pro nepřijaté hovory se v běžné mluvě používají zavedená označení podle toho, kdo
hovor ukončil. Tabulka je neuvádí jako hodnotu, ale poznáte je ze sloupců **Směr**
a **Hovor ukončil**:

- **zmeškaný** — příchozí hovor, který **nikdo nepřijal** a volající zavěsil
- **odmítnutý** — odchozí hovor, který **volaný** odmítl
- **zrušený** — odchozí hovor, kde během vyzvánění zavěsil **volající**

### Celková délka a délky částí hovoru

Šest sloupců s délkami, všechny v sekundách. Co přesně která délka měří, vysvětluje 
sekce [Délka hovoru a jeho částí](#délka-hovoru-a-jeho-částí).

- **Celková délka** — Délka hovoru jako celku. Nezahrnuje délku sestavení ani délku
  pohovorové interakce.
- **Délka sestavení** — Jak dlouho ústředna volala zařízením volajícího u funkce klikni
  a volej. U ručně vytočených hovorů je zde nula.
- **Délka uvítání** — Jak dlouho běžely úvodní hlášky a rozcestník, než se začalo
  vyzvánět příjemcům.
- **Délka vyzvánění** — Jak dlouho se hovor snažil dostat k příjemcům, tedy do jeho
  přijetí nebo ukončení.
- **Délka konverzace** — Jak dlouho byli v hovoru oba účastníci. Nezahrnuje pohovorovou
  interakci.
- **Délka pohovorové interakce** — Jak dlouho hovor pokračoval po zavěšení lokální
  strany, například automatem pro hodnocení hovoru.

Nula znamená, že příslušná část hovoru nenastala — nebo že byla tak krátká, že po
zaokrouhlení na celé sekundy vyšla nulová.

### Hovor ukončil

Která strana hovor ukončila.

- **volající** — zavěsil účastník, který volal
- **volaný** — zavěsil účastník, který hovor přijal
- **systém** — hovor ukončila ústředna — automaticky, na žádost uživatele zadanou
  v uživatelském rozhraní nebo na žádost aplikace třetí strany zaslané přes API.

### Původ ukončení

Informace o identitě toho, kdo hovor ukončil.

V buňce může být:
- telefonní číslo, které zavěsilo, např. `+420 604 110 265`.
- číslo klapky a jejího zařízení, které zavěsilo, např. `900 (900-1)`.
- `UI` — hovor ukončil uživatel v aplikaci OptimCall
- `API` — hovor ukončila jiná aplikace přes API rozhraní ústředny
- text chyby — hovor skončil chybou a tohle je její popis

Prázdná buňka znamená, že není koho uvést, hovor ukončila ústředna sama podle
některého z pravidel

### Země

Země protistrany, jako dvoumístný kód (`CZ`, `SK`, `DE`, …).

- **Příchozí hovor** → země volajícího.
- **Odchozí hovor** → země volaného.
- **Interní hovor** nebo číslo, u kterého zemi nelze určit → `---`.

### Klikni a volej

Obsahuje hodnotu

- `ano`, pokud bylo volání zahájeno pomocí **klikni a volej**
- `ne`, pokud byl hovor vytočen ručně

### Nahrávka

Obsahuje hodnotu

- `ano`, pokud byl hovor nahráván
- `ne`, pokud hovor nebyl nahráván

### Poznámka

Text, který k hovoru **ručně vepsal uživatel** v aplikaci OptimCall — třeba čeho se hovor
týkal. Nemá-li hovor poznámku, je buňka prázdná.
