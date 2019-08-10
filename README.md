 # sphere-design
Design assumptions for the programming language - sphere

# version 0.2
## General assumptions:
1. Object programming language
2. Inspired by C, C#,  Java, Rust
3. Flexible syntax depending on the context:
	* Strong types by default, but in some context they can be dynamically
	* GC can by disabled in some context and manual memory management can by used.
4. Depend on JIT/VM.
5. Source/binary are in AST
6. Handling simple reflections
7. Generic types
8. CVC - Code View Controller

## Project assumptions:
1. Multiple inheritance allowed


# wersja 0.1
Założenia projektowe do języka programowania sphere

Założenia ogólne:
1. Obiektowy język programowania
2. Statycznie typowany z możliwością wykonywania dynamicznego kodu
3. Kod przechowywany jest jako drzewo reprezentacji kodu (AST/syntax tree). Kod przechowywany jest w bazie danych a nie w plikach.
4. możliwość uruchomienia na różnych platformach (x86, ARM, Atmel)
5. model MVC w IDE – w zależności co robimy możemy w różny sposób wyświetlać kod. Sam kod zawsze jest zapisywany w postacie drzewa reprezentującego kod, a podczas edycji przetwarzany jest do pliku w odpowiednim formacie.
6. Główna składnia C# z domieszką Assembler, Java, Python, C++. Drzewo reprezentujące kod rozszerzeniem drzewa C#, tak żeby móc zapisywać kod w Java, C++ i Pythonie. Na razie w bardzo ograniczonej wersji.
7. w zależności od potrzeb, może być interpretowany lub kompilowany lub mieszany (jedna funkcja kompilowana inna interpretowana).
8. możliwość mieszania różnych kodów w ramach jednego obiektu.
9.  na początek, prędkość nie jest najważniejsza. Ważniejsze jest: szybkość pisania i łatwość czytania kodu oraz obsługa różnych platform.

Założenia projektowe:
1) Wszystkie obiekty dziedziczą z głównej klasy Object
2) Wszystkie obiekty są serializowane i obsługa serializacji jest obsługiwana w klasie Object
3) Serializowane są też funkcje – serializowana funkcja, to jej drzewo reprezentacji (AST) która może być interpretowana lub kompilowana.
4) Kilka poziomów programowania i dostępu do sprzętu.
	a) wysoko poziomowy – jest c#
	b) wskaźniki – jak C++
	c) assembler
5) Dostęp do zasobów komputera i systemu przez instancję kontekstu wykonania, który jest obiektem zawierającym interfejsy do systemu plików, sieci, gui, do zarządzania pamięcią i innych.
Celem takiego podejścia jest możliwość przełączania się między w trakcie wykonania między różnymi implementacjiamii (np. system plików przez Internet)

Główne obiekty i klasy:
- Object – zawiera wszystko to co c# i mechanizmy do serializacji (wszystko serailizujemy)
- KontekstWykonania - zawiera wszystkie potrzebna obiekty
	- kontekst systemowy - system plików, sieć,Gui..
	- kontekst fizyczny - pamięć, procesy, wątki...
	- dane wykonującego /uprawnienia itp
	
