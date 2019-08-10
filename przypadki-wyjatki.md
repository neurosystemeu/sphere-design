# For version 0.2
Hello world example
``` C#
public void main()
{
  println("Hello world");
}
```

Serialization - object and code
``` C#
public class Person
{
  string Name {get;set;}
  string Surname {get;set;}
  
  string SomeMethod(string someParam)
  {
    return someParam + " text";
  }
}

public void main()
{
  var person = new Person();
  println(person); 
  
  var stringWithSerialisationofObject = person.Serialize();
  var newInstanceOfPerson = Object.Deserialize(stringWithSerialisationData);
  
  var stringWithSerialisationOfType = Type.Serialize(typeof(Person));
  var newType = Type.Deserialize(stringWithSerialisationOfType);
  var newTypeInstance = Type.Create<Interface/type>(newType, typ); 
  dynamic d = newTypeInstance;
  d.SomeMethode("sdf"); 
}
```

Execution context
``` C#
//simple dynamic context without gc
public void main()
{
  println("some text"); //execute context.println
  
  //create new context, and push on context stack
  using(var newContext = new WithoutGCContext()) //use dynamic contex
  {
    //here all code execute on newContext
    println("Helo from new context"); //newContext.println(...)
    someFunction(); //use newContext
    
    var person = new Person(); //execute newContext.new
  }  
}

//static contex with memory owner - use in compile time
public void main()
{
  println("some text"); //execute context.println
  
  //create new context, and push on context stack
  using(var newContext = new ContextWithMemoryOwner()) //use static context used in compile time
  {
    var person = new Person(); //execute newContext.new - return uniqe<Person>
    var s = someFunction(person); //move responsibility of object to function
    
    print(person); //Compile error - person lost ovner
    print(s); //ok
    
    someFunction(person.Copy());//person now is share and both main and somefunction responsibility for it
    
    //here all code execute on newContext
    println(s); //newContext.println(...)     
    
  }  
}

//static context with manula memory management - compile time
public void main()
{
  println("some text"); //execute context.println
  
  //create new context, and push on context stack
  using(var newContext = new ContextWithManualMemoryManagement ()) //use static context used in compile time
  {
    var person = new Person(); //execute newContext.new - return Person*
    var s = someFunction(person); //move responsibility of object to function
    
    print(person); //Compile error - person lost 
    print(s); //ok
    
    //here all code execute on newContext
    println(s); //newContext.println(...)
  }  
}
```

Exceptions
``` C#
string someMethod(int i)
{
  if( i > 0)
  {
    return "positive";
  } else
  {
    return Error(new Exception("Some descriptions"));
    //or
    throw new Exception("Some descriptions");
    //two above method are interpreted the same
  }
}

string anotherFunction()
{
  return someMethod(-1);
}

string bFunction()
{
  someMethod(-1); //this throw exception, in default contex this return error and exit function;
  //if executet in fast unsafe contex, line abowe not throw exception until explicity unwrap.
  
  //if fast unsafe context is use, expilicty unwrap is needed to handle error
  someMethod(-1).unwrap(); //if error return error;
  return someMethod(1);
}

void main()
{
  var ret = someMethod();
  ret.IsError() ? println("Error");//print error
  
  //or
  try
  {
    var ret = someMethod();
    println(ret);
  }catch()
  {}
}


```

# For version 0.1

## Przypadki użycia - wyjątków i ich obsługa

Wyjątki nie są takie wyjątkowe :), zwłaszcza w świecie mikrokontrolerów, gdzie w warunkach dużych zakłuceń odczytanie np. temperatury z czujnika często kończy się błędem.
Jednym słowem, wyjątków w rozumieniu C# jest znacznie więcej niż to się wydaję - patrząc na strukturę kodu.
Obsługa jednego wyjątku zajmuje dużo kodu w C#.
Idealnie powinniśmy mieć możliwość w jakiś ładny sposób zapisywania wszystkich możliwych wyjątków, wykonywanie powtórzeń przy błędach transmisji
oraz wykonanie "wyjątku" np. po 5 błędnych próbach.

## Powtarzanie wykonania kodu
Przykładowy kod z uwagami

``` C#
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
``` C#
  var temperatura = wykonajNRazy(czujnik.PobierzTemperature(),5, new Exceptions("Czujnik ma poważny problem i nie działa po 5 próbach")); 
```

> Składnia

> Możliwość przekazania funkcji, jako parametr funkcji.
> jakaśFunkcjaWykonująca(&obiekt.funkcjaObiektu("jakiś parametr tekstowy"), jakiśParametrFunkcji-jakaśFunkcjaWykonująca)

Chcemy tu uzyskać referencje do funkcji danego obiektu - tylko nie jako delegat. Chcemy żeby ta konkretna funkcja z tymi konkretnymi parametrami 
była przekazana jako obiekt, który potem wykonujemy (bez podawania parametrów)
