# jmeter_warsztaty
Materiały do/z warsztatów JMetera przeprowadzonych na Testwarez 2019

# Przypadki testowe do zautomatyzowania
## REST - Wysyłanie wiadomości
### Wiadomość z JSON body
Kroki:
  1. Wyślij wiadomość POST na http://{serwer}:{port}/api/message z body w formacie JSON:
     ```json
     {
        "message": "{wiadomość}",
        "teamColor": "{kolor zespołu}"
     }
     ```
  1. Zapytaj o wiadomość z id otrzymanym w wyniku zapytania z kroku 1 GET http://{serwer}:{port}/api/message?id=
  1. *Co 100 wysłanych wiadomości zapytaj system o wszystkie wiadomości GET http://{serwer}:{port}/api/messages/{kolor zespołu}
  
### Wiadomość z użyciem parametrów
Kroki:
  1. Wyślij wiadomość POST na http://{serwer}:{port}/api/parametrized-message?team={kolor zespołu}&message={wiadomość}
  1. Zapytaj o wiadomość z id otrzymanym w wyniku zapytania z kroku 1 GET http://{serwer}:{port}/api/message?id=
  1. *Co 100 wysłanych wiadomości zapytaj system o wszystkie wiadomości GET http://{serwer}:{port}/api/messages/{kolor zespołu}    

## SOAP - Imiona kotów
### Sprawdź dane kota po imieniu
Dane wejściowe:
  - Lista kocich imion: Filemon, Fryderyk, Klakier, Mruczek, Ada, Stefan
  - Plik wsdl dostępny na: http://{serwer}:{port}/soap/cats.wsdl

Kroki:
  1. Wykonaj 100 zapytań do webserwisu soap używając pseudolosowego wyboru imienia kota oraz wpisując kolor swojego zespołu na http://{serwer}:{port}/soap 
     Nazwij krok testowy tak, aby w raporcie możnabyło łatwo zagregować wyniki według kociego imienia
    

## WebSocket - Zakup biletu lotniczego
Zamów najtańszy bilet lotniczy na danej trasie. 

### Kroki testu
  1. Zapytaj o dostępne lotniska: `ws://{serwer}:{port}/get_cities`
  1. Zapytaj o loty z wybranego (losowego) lotniska do innego wybranego (losowego) lotniska: `ws://{serwer}:{port}/get_flights`
     - treść message'a musi mieć następującą formę: `{lotnisko wylotu}->{lotnisko przylotu}`
  1. Odbierz wszystkie dostępne loty (loty kończą się messagem o treści `END`
  1. Wyśli zlecenie zakupu najtańszego lotu 
     - treść message'a musi mieć następującą formę `{team}:buy:{timestamp lotu}:{cena}`

W ostatnim message'u `{timestamp lotu}` i `{cena}` muszą być zgodne z najtańszą opcją przedstawioną przez serwer. Dopiero wtedy request zostanie zaliczony i pojawi się w statystykach. `{team}` oznacza drużynę uczestnika warsztatów 'red' albo 'blue'.

## JMS - Zamawianie kawy
### Przejdź proces zamówienia kawy (zamówienie, odbiór)
Kroki:
  1. Wyślij do kolejki zamówienie kawy do kolejki coffee.in tcp://{server}:{port}
  user: system
  pw: podamy na zajęciach
  (ew po HTTP: GET http://{serwer}:{port}/jms/publish ) z JSON body:
     ```
     {
       "name":"{nazwa kawy}", 
       "teamColor":"{kolor zespołu}"
     }
     ```
  1. Odbierz z kolejki coffee.out zamówienie 

## Plik - Wysyłanie przelewów
### Cel testu
Zapisać w podanym katalogu udostępnionym plik zawierający przelewy. Każdy przelew jest na 1PLN, więc kwoty nie podajemy.  
Plik składa się z następujących linii:
`{<nr konta źródłowego>:<nr konta docelowego>:<suma kontrolna>}`
  - `<nr konta źródłowego>`, `<nr konta docelowego>` - sześciocyfrowa liczba; jeżeli pierwsza cyfra jest parzysta to bank nalezy do Niebieskich, a jeżeli nieparzysta, do bank należy do Czerwonych
  - `<suma kontrolna>` - suma cyfr konta źródłowego i docelowego, taka że cyfry z parzystych miejsc (numerowanych od lewej od 1) mnożone są przez 3, a nieparzyste przez 7 i suma ta jest wzięta modulo 27  
    
Ostatnia linia pliku ma następującą postać:
`<liczba przelewów w pliku>:<suma kontrolna pliku>`
  - `<suma kontrolna pliku>` - wyliczona tak suma kontrolna przelewu, ale na skonkatenowanych wszystkich sumach kontrolnych przelewów

Nazwa pliku powinna być utworzona według wzorca `<UUID>.txt`

### Zadanie
Napisać skrypt, który będzie generował 100 przelewów na 15s dla całego zespołu. Pliki są pobierane z serwera co 15s i wtedy przeliczane. 
Wygrywa zespół, który będzie najbliżej zadanej wartości. Wykres pokazuje o ile bliżej celu jest dany zespół od drugiego zespołu. 

## Baza danych
Zajrzyj do dragon_README.md
