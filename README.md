# jmeter_warsztaty
Materiały do/z warsztatów JMetera przeprowadzonych na Testwarez 2019

# Przypadki testowe do zautomatyzowania
## REST
  1. Wyślij wiadomość z użyciem JSON body (+sprawdzenie czy wiadomość się dodała po ID)
  1. Wyślij wiadomość z użyciem parametrów (+jw)
  1. Sprawdź wiadomości

## SOAP
Sprawdź dane kota po imieniu

## WebSocket

## JMS
Przejdź proces zamówienia kawy (zamówienie, odbiór)

## Plik
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
