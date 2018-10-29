# Przypadki użycia - wyjątków i ich obsługa

Wyjątki nie są takie wyjątkowe :), zwłaszcza w świecie mikrokontrolerów, gdzie w warunkach dużych zakłuceń odczytanie np. temperatury z czujnika często kończy się błędem.
Jednym słowem, wyjątków w rozumieniu C# jest znacznie więcej niż to się wydaję - patrząc na strukturę kodu.
Obsługa jednego wyjątku zajmuje dużo kodu w C#.
Idealnie powinniśmy mieć możliwość w jakiś ładny sposób zapisywania wszystkich możliwych wyjątków, wykonywanie powtórzeń przy błędach transmisji
oraz wykonanie "wyjątku" np. po 5 błędnych próbach.

## Powtarzanie wykonania kodu
Przykładowy kod z uwagami

```
//C#
var czujnik = new Termometr();
int iloscBledow=5;
while(iloscBledow>0)
{
  try
  {
    czujnik.PobierzTemperature();
    break;
  } catch()
  {
    //powtażamy
    iloscBledow--;  
  }
}
if(iloscBledow <=0)
{
  throw new Exceptions("Czujnik ma poważny problem i nie działa po 5 próbach"); 
  
  
}

```

Swoją drogą, co może zrobić główna funkcja sterownika po takim wyjątku?
może wyłączyć wszystko, odczekać i spróbować jeszcze raz i drugi raz,
a jeśli to nie pomoże to zakładamy że czujnik nie działa i zgłaszamy awarię systemu (lub pracujemy bez czujnika jeśli to możliwe).
słowem gdzieś wyżej będzie podobna pętla..

a może wystarczyło by
```
  var temperatura = wykonajNRazy(czujnik.PobierzTemperature(),5, new Exceptions("Czujnik ma poważny problem i nie działa po 5 próbach")); 
```

> Składnia

> Możliwość przekazania funkcji, jako parametr funkcji.
> jakaśFunkcjaWykonująca(&obiekt.funkcjaObiektu("jakiś parametr tekstowy"), jakiśParametrFunkcji-jakaśFunkcjaWykonująca)

Chcemy tu uzyskać referencje do funkcji danego obiektu - tylko nie jako delegat. Chcemy żeby ta konkretna funkcja z tymi konkretnymi parametrami 
była przekazana jako obiekt, który potem wykonujemy (bez podawania parametrów)
