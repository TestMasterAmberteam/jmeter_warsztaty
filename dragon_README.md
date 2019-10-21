# Uruchomienie
`python dragon.py`



# Reguły gry
 
## Smok
Smoki są dwa `red` oraz `blue`. Na początku gry mają 1M HP. 

Smoki w każdej turze zadają losowo od 100 do 1000 obrażeń. Smok strzela kulą ognia, która uderza w graczy "po kolei" i zabija graczy dopóki nie straci mocy.  

## Gracze
Gracze są podzieleni na trzy klasy:
  - rycerz - `tank` - atakuje 1 DMG; 100HP
  - uzdrowiciel - `heal` -  nie atakuje, może uzdrawiać graczy ze swojej drużyny; 40HP
  - mag - `wzrd` - wyszukuje słabe punkty smoka i atakuje je czarami; 30HP
  
Żeby zagrać graczem, to trzeba go wprowadzić do tabeli player:
```sql
CREATE TABLE public.player
(
    id integer NOT NULL DEFAULT nextval('player_id_seq'::regclass),
    name character varying(50) COLLATE pg_catalog."default" NOT NULL,
    class character varying(4) COLLATE pg_catalog."default" NOT NULL,
    hp integer NOT NULL DEFAULT 1,
    team character varying(8) COLLATE pg_catalog."default" NOT NULL DEFAULT 'red'::character varying,
    CONSTRAINT player_pkey PRIMARY KEY (id),
    CONSTRAINT uq_name UNIQUE (name)
)
```

Insert do tabeli `player` nadaje graczowi HP zgodne z jego klasą.

## Atak
Atak odbywa się przez insert do tabeli `attack`
```sql
CREATE TABLE public.attack
(
    player integer NOT NULL,
    type character varying(4) COLLATE pg_catalog."default" NOT NULL,
    param character varying(32) COLLATE pg_catalog."default",
    CONSTRAINT attack_pkey PRIMARY KEY (player),
    CONSTRAINT fk_player FOREIGN KEY (player)
        REFERENCES public.player (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE NO ACTION
        NOT VALID
)
```
Maksymalnie może atakować 1000 graczy. Więcej nie mieści się do pieczary smoka. 
Ich ataki i uzdrowienia są ignorowane. 

### Typy ataków dla klas
UWAGA: Atakowanie typem nieprzypisanym do danej klasy jest nieskuteczne. 

#### Rycerz
Rycerz atakuje z siłą 1DMG. Kod jego ataku to `hit`. Parametry są ignorowane.

#### Uzdrowiciel
Uzdrowiciel nie atakuje, ale może uzdrawiać współtowarzyszy atakujących.
Kod jego "ataku" to 'heal'. Daje +10HP dla jednego losowego gracza biorącego udział w ataku. 

#### Mag
Mag wypatruje słabych punktów smoka (tablica `weak_spot`) i rzuca zaklęcia żeby jak najbardziej osłabić smoka.
Smok jest cały czas w ruchu podczas walki, więc co turę pojawiają się nowe słabe punkty, a te z poprzedniej tury znikają. 
```SQL
CREATE TABLE public.weak_spot
(
    type character varying(4) COLLATE pg_catalog."default" NOT NULL,
    description character varying(4096) COLLATE pg_catalog."default" NOT NULL
)
```

##### Słabe punkty i zaklęcia działające na nie
###### Przerwa między łuskami / Abrakadabra - 25 DMG
Kod podatności to `scal`. Żeby skutecznie rzucić zaklęcie *Abrakadabra* należy wykonać atak o kodzie `abra` z parametrem
równym MD5 opisu miejsca dziury (kolumny `description`)

###### Smok głodny / Baran - 50 DMG
Kod podatności to `hngr`. Atak polega na wyczarowaniu barana wypchanego siarką (kod ataku `ram`). Jako parametr ataku 
należy podać timestamp wyprodukowania barana, bo smok za starego nie zje (max 1 tura), a jeżeli baran bęszie "z przyszłości", 
to pozna oszustwo i też się nim nie zainteresuje. 

### Historia ataków
W systemie przechowywana jest hitoria ataków, tak żeby implementujący skrypt mógł sprawdzić skuteczność zaimplementowanych ataków. 
```sql
CREATE TABLE public.attack_history
(
    player integer NOT NULL,
    type character varying(4) COLLATE pg_catalog."default" NOT NULL,
    param character varying(32) COLLATE pg_catalog."default" NOT NULL,
    moment integer NOT NULL,
    damage integer NOT NULL
)
```

## Konto bazodanowe
Konto, którym można się połączyć do bazy to `player` hasło `//24|soon|POEM|tiny|57//`

Konto `player` ma następujące uprawnineia
  - tabela `player`
    - insert
    - select na kolumnach `id`, `hp`
  - tabela `attack`
    - insert
    - select na kolumnie `player`
  - tabela `weak_spot`
    - select
