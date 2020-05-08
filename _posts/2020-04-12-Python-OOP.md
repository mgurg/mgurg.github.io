---
layout: post
title: "Python: OOP"
categories: Python
author: "Michał"
---

# OOP


KLASA - jest "ideą", "schematem", "wyobrażeniem" właściwości (zmienne) i interfejs (metody)

INSTANCJA - jest "powołanym do życia" obiektem, który zawiera określone przez klasę właściwości. Można mieć kilka instancji jednej klasy

Do stworzenia klasy potrzebujemy:

- nazwy klasy

- zdefiniować właściwości klasy

Używanie klasy polega na:

- utworzeniu nowej instancji obiektu
- wykonywaniu operacji na instancji

---

Zalety OOP:

-	tworzenie jednorodnego pakietu, zawierającego dane oraz sposoby manipulowania nimi
-	umożliwiają podejście - divide and conquer (dziel i zwyciężaj)
  -	można testować zachowanie każdej z klas oddzielnie
  -	zwiększa modularność, zmniejsza kompleksowość
-	klasy ułatwiają ponowne użycie kodu
  - każda z klas tworzy oddzielne "środowisko" - różne klasy mogą mieć takie same nazwy funkcji
  
  - dziedziczenie pozwala aby podklasa, zredefiniowała lub rozszerzyła wybrane właściwości klasy nadrzędnej
  
    

Samochody mają **dane**, jak na przykład liczba kół, drzwi, oraz ilość miejsc siedzących. Wykazują one również **zachowanie**: mogą przyspieszyć, zatrzymać się, pokazać ile paliwa zostało oraz wiele innych.

W programowaniu **object-oriented dane** identyfikujemy jako atrybuty, a zachowanie jako **metody**.

*Dane → Atrybuty i Zachowanie → Metody* 

Przykład prostej klasy:

```python
class Samochod(object):
	# definicje danych
	# definicje metod
```
class – podobnie jak def
- słowo object oznacza, że Samochód jest obiektem w Python (object) i dziedziczy z niego wszystkie właściwości
  - Samochod jest podklasą object
  - object jest klasą nadrzędną dla Samochod

Definiowanie konstruktora:

```python
class Samochod(object):
	def __init__(self,marka,model) # double under - dunder
    	self.marka = marka # atrybuty każdej instancji obiektu Samochod
        self.model = model
```

self - referencja instancji

marka, model - dane inicjalizujące

Definiowanie metod:

```python
def accelerate(self, value):
	self.speed += value 
```

Metody specjalne:

```python
OPERATORY:
+, -, ==, <, >, len(), print, i in.
__add__(self, other) # -> self + other
__sub__(self, other) # -> self -other
__eq__(self, other)  # -> self == other
__lt__(self, other)  # -> self < other
__len__(self) 		 # -> len(self)
__str__(self) 		 # -> print(self)
```

---

Paradygmaty OOP:

-	abstrakcja  – uproszczenie problemu
-	enkapsulacja (HERMETYZACJA) – ukrycie składowych
-	dziedziczenie  – mechanizm współdzielenia funkcjonalności
-	polimorfizm  –  wielopostaciowość

### **Dziedziczenie**

Dziedziczenie umożliwia tworzenie klas, które korzystają z atrybutów klas nadrzędnych (superklasa / rodzic).
Klasy dziedziczące (podklasy / dzieci) mogą część atrybutów mieć zdefiniowanych według własnych potrzeb.

```python
class Zwierze(object):
	# definicje danych
	# definicje metod

class Czlowiek(Zwierze):
	# definicje danych
	# definicje metod

class Student(Czlowiek):
	# definicje danych
	# definicje metod
```

```python
isinstance(obiekt, klasa) # sprawdza czy dany obiekt jest instancją klasy
issubclass(klasaA, klasaB) # sprawdza czy klasaA jest podklasą klasy B
```

Klasa może dziedziczyć z wielu klas



**Pola klasy** - Zmienne definiowane na poziomie klasy. Nie używamy słówka self 

Służą do przechowywania danych niezależnych od instancji (wspólne dla wszystkich instancji)



**Metody klasy** - Metody, które jako pierwszy argument przyjmują klasę zamiast
instancji.

Używamy dekoratora *@classmethod* nad definicją metody. 

Pierwszy argument to słowo kluczowe *cls*
Możemy używać jako alternatywne konstruktory

```python
@classmethod
def my_class_method(cls):
	pass
```



**Metody statyczne** - Metody, które nie przyjmują ani instancji ani klasy jako argument.
Wyglądają jak normalne metody

Używamy dekoratora *@staticmethod* nad definicją metody.

Używamy je gdy przekazanie jakiejś informacji nie wymaga tworzenia instancji klasy. (matematyczne)

```python
@staticmethod
def my_static_method():
	pass
	
MyClass.my_static_method()
```



### enkapsulacja

Python daje możliwość stworzenia pseudo-prywatnych pól i metod.

Do nazwy (pola, metody) dodajemy dwa podkreślniki tylko z przodu. Można je użyć wewnątrz klasy, ale poza nią są niewidoczne – ale można i tak ich użyć!

```python
self.__moje_pole_prywatne
def __metoda_prywatna(self, arg1):
self.__moje_pole_prywatne = arg1
```

**namespace**

Namespace jest obszarem nazw, które są dostępne dla klasy. W ten sposób możemy znaleźć pseudo-prywatny atrybut

```python
print(MojaKlasa.__dict__)
print(instancja.__dict__)
```



**Properties** – właściwości definiujemy jak metody z dekoratorem, z nazwą identyczną jak zmienna,
służą do manipulowania zmiennymi w kontrolowany przez nas sposób.

Używamy jak zmienne!

Getter – służy do zwrócenia wartości ze zmiennej

```python
self.__imie
@property
def imie(self):
	return str(self.__imie).capitalize()
```

Setter – służy do zapisania wartości do zmiennej – daje możliwość do kontrolowania tego co zapisujemy
(musi najpierw być zdefiniowany getter)

```python
self.__imie
@imie.setter
def name(self, value):
	self.__imie = value
```

Deleter – służy do usuwania zawartości zmiennej w kontrolowany sposób

```
self.__imie
@imie.deleter
def name(self):
	self.__imie = None
```



---



```python
class Patient(object):
	''' Medical centre patient'''
	status = 'patient'
	def __init__(self,name,age):
		self.name = name
		self.age = age
		self.conditions = []

	def get_details(self):
		print(f'Patient record: {self.name}, {self.age} years.' \
		f' Current information: {self.conditions}.')

	def add_info(self,information):
		self.conditions.append(information)
```
Stworzenie obiektów: 
```python
steve = Patient('Steven Hughes',48)
abigail = Patient('Abigail Sandwick',32)
```



```python
class Infant(Patient):
	''' Patient under 2 years'''
	def __init__(self,name,age):
		self.vaccinations = []
		super().__init__(name,age)

	def add_vac(self,vaccine):
		self.vaccinations.append(vaccine)
		
	def get_details(self):
		print(f'Patient record: {self.name}, {self.age} years.' \
		f' Patient has had {self.vaccinations} vaccines.' \
		f' Current information: {self.conditions}.' \
		f'\n{self.name} IS AN INFANT, HAS HE HAD ALL HIS CHECKS?')
```
Stworzenie "dzieci":
```python
archie = Infant('Archie Fittleworth',0)    
archie.add_vac('MMR') 
```




```python
class Patient(object):
	''' 
	Attributes
	----------
	name: Patient name
	age: Patient age
	conditions: Existing medical conditions
	'''
	status = 'patient'
	
	def __init__(self,name,age):
	self.name = name
	self.age = age
	self.conditions = []

	def get_details(self):
		print(f'Patient record: {self.name}, {self.age} years.' \
		f' Current information: {self.conditions}.')

	def add_info(self,information):
		self.conditions.append(information)
```