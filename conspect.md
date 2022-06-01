# C++

## Атрибуты

### C++11

[[noreturn]]

Функция помеченная так не должна возвращать поток управленения.

[[carries_dependencies]]

Атрибут связан с моделями памяти.

### C++14 

[[depracated]]

Атрибудт позволяет разметить устаревший код, вызывая warning'и при его использовании.

```cpp
struct [[depracated]] Name;
[[depracated]] typedef S* pS;
using PS [[depracated]] = S*;
[[depracated]] int x;
uninon U { [[depracated]] int n; }
[[depracated]] void f();
namespace [[depracated]] {NS { int x; }
enum [[depracated]] E {};
enum E { a [[depracated]], b [[depracated]] = 1 };
template < > struct [[depracated]] X<int> {};
```

### C++17

[[fallthrough]] 

Используется для switch блоков, сообщая что оператор break не был пропущен по ошибке.

```cpp
switch (x)
{
	case 1:
	    [[fallthrough]] //No warning
	case 2:
	    break;
    case 3: //Warning
    case 4:
        break; 
}
```


[[nodiscard]] 

Атрибут требует чтобы результат функции не был проигнорирован.

```cpp
[[nodiscard]] bool isEmpty() { ... }

bool status = isEmpty(); //No warning

isEmpty(); //Warning - результат возвращаемый функцией проигнорирован
```

[[maybe_unused]] 


Атрибут убирает warning от неиспользуемых аргументов\переменных\функций итд.

```cpp
struct [[maybe_unused]]  S;
[[maybe_unused]]  typedef S* PS;
using PS [[maybe_unused]]  = S*;
[[maybe_unused]]  int x;
union U { [[maybe_unused]]  int n; };
[[maybe_unused]]  void f();
enum [[maybe_unused]]  E {};
enum { A [[maybe_unused]], B [[maybe_unused]] }; 
```


## Lambda

### C++11

Анонимные функции, вызываемого типа std::function, могут использоваться в STL.

Общий вид:
```cpp
auto lamda = [capture-list](arguments) mutable -> ret_type
{
    ...
}; //Создание

//Если не указывать mutable - по дефолту он не включен

lambda(arguments); //Вызов
```

Списки захвата:
```cpp
[] // ничего не захватывается
[=] // локальные переменные по значению
[&] // локальные переменные по ссылке
[this] // this по ссылке
[a, &b] // захват отдельных перменных, по значению и ссылке
```

### C++14

Дополнены правила списка захвата:

```cpp
[&r = x, x = x + 1]
//в lambda можно захватить ссылку, и назвать её как удобно, и можно использовать выражение для инициализации переменной

[x = factory(2)]
[p = std::move(p)]

//Пример генератора
auto generator = [x = 0]() mutable { return x++; }
int a = generator(); // == 0
int b = generator(); // == 1
```

Так же перестал быть необходим trailing return type, для возвращаемого типа auto.

Были введены генерализированные lambds, когда аргументы указаны типа auto.

### C++17

Добавлена возможность захвата текущего объекта по копии, а не по ссылке.

```cpp
[*this]
```


## POD-type

Plain old data - структура размещающаяся в памяти таким образом, как её описал программист, исключая оптимизации. Это может быть необходимо для передачи данных в другие языки программирования.

POD = Тривиальный класс + Класс со стандартным размещением


Тривиальный класс:
 
+ T() = default;
+ T(const T&) = default;
+ T& operator=(const T&) = default;
+ T(T&&) = default;
+ T& operator=(T&&) = default;
+ ~T() = default;
+ Нет виртуальных методов и виртуального наследования
+ Все нестатические поля тривиальны
+ Все базовые классы тривиальны(при наличии)


Класс со стандартным размещением:

+ Все нестатические поля имеют одинаковый доступ private\public\protected
+ Нет вирутальных методов и вирт. наследования
+ Нет нестатических полей-ссылок
+ Все нестатические поля и базовые классы со стандартным размещением
+ Все нестатические поля объявленны в одном классе в иерархии наследования
+ Нет базовых классов того же типа, что и первое нестатическое поле


## auto/decltype

auto - возможность замена типа на auto.

Примеры типов: переменной, возвращаемого значения функции, и шаблоных аргументов.

decltype() - позволяет выводить тип переменной или выражения.

### C++11

Особенности работы auto:

```cpp
int bar();

auto i = 0; //int
auto ui = 0u; //unsigned int
volatile auto ci = i; //volotile int
const volatile auto cvi = i; // const volatile int
auto j = cvi; //int

auto& ri = i; //int &
const auto& cri = i; //const int&

auto&& fri = i; // int &
auto&& fcri = cri; // const int &

auto &&frv = 0; // int &&
auto &&frvf = bar(); // int &&
```


#### Альтернативный синтаксис шаблонных функций

Позволяет выводить возвращаемый тип шаблонной функции.

```cpp
template <typename T1, typename T2>
auto sum(const T1& lhs, const T2& rhs) -> decltype(lhs + rhs)
{
	return lhs + rhs;
}
```

## C++14

Не нужен trailing return type, достаточно auto, 
Можно реализовать функцию факториал с возвращаемым типом auto, но факториал от нуля должен быть определён до рекурсивного использования этой функции.


Так же было осущестлвенно послабление, теперь внутри decltype() можно указывать не выражение\переменную, а auto.

Примеры:

```cpp
double foo();
double&& bar();

double v1 = 0.0;  //double
const double& v2 = v1;  //const double &

decltype(auto) v3 = v1;  //double
decltype(auto) v4 = (v1);  //double& 
decltype(auto) v5 = v2;  //const double&

decltype(auto) v6 = foo(); //double
decltype(auto) v7=bar(); //double && 
```


## Literals

### C++11

#### Строковые литералы

```cpp
//Было до С++11

"Text" //char 
L"Text" //wchar_t

//Появилось в C++11 - utf

u8"Text" //char - utf8
u"Text" //char16_t
U"Text"//char32_t

//Сырые строки обрамляются в () в "" и могут иметь произвольны delemiter
R"delimiter( raw string )delimeter" 
LR"delimiter( raw string )delimeter"
u8R"delimiter( raw string )delimeter"
uR"delimiter( raw string )delimeter"
UR"delimiter( raw string )delimeter"
```



### Пользовательские литералы

Пример пользовательского литерала преобразования радиан в градусы.

```cpp
long double operator""_degrees(long double value)
{
	return value * M_PI / 180.0;
}

double degrees = 0.38__degrees
```

Список возможных аргументов, при определении пользовательского литерала:

```cpp
( const char * )
( unsigned long long int )	
( long double )	
( char )
( wchar_t )	
( char16_t )	
( char32_t )
( const char * , std::size_t )	
( const wchar_t * , std::size_t )	
( const char16_t * , std::size_t )	
( const char32_t * , std::size_t )
```


### C++ 14

#### Строковый литерал

```cpp

std::string from_literal = "some string"s;

```

#### Бинарные литералы

```cpp
int a = 0b111; // == 7
int b = 0B11; // == 3
```

#### Разделители числовых литералов

```cpp
int a = 1'000'000;
int b = 3.14'15'92'65;
```

#### STL литералы

```cpp
	auto half_minute = 30s; // std::chrono::duration
	auto day = 24h; // std::chrono::duration

	auto complex = 1 + 1i; //std::complex
```


## Initialization

### C++11

#### Универсальная инициализация

Везде можно использовать {}:

```cpp
// До С++11

int a;     		//(1) default init
int b(2);  		//(2) direct init
int c = 2; 		//(3) copy init
int d = int();  //(4) value init
int arr[] = {1, 2, 3}; //(5) aggregate init

struct Point { double x, y; } point (0.0, 0.0); //(5)
std::complex<double> cmpl(0.0, 0.0); //(2)
std::complex<double> c2 = std::complex<double>(0.0, 0.0); //(3)

// Начиная с C++11 можно везде {}

int a;
int b{2};
int c = {2};
int d{};
int arr[] = {1, 2, 3};

struct Point { double x, y; } point {0.0, 0.0}; //(5)
std::complex<double> cmpl{0.0, 0.0}; //(2)
std::complex<double> c2 = std::complex<double>{0.0, 0.0}; //(3)
```

#### std::initializer_list

Возможность использовать список инициализации для создания конструкторов или операторов присвоения.

Значения задаются между {} и через запятую.

initializer_list содержит следующие функции:

```cpp
init_list.size()
init_list.begin()
init_list.end()

init_list.r/c/begin/end() // Начиная с C++14

init_list.empty() // Начиная с C++17
init_list.data() // Начиная с C++ 17
```

### C++14

#### Aggregate initialization with deafult member initializer

```cpp
struct x 
{
	int a,b;
	char c = '0'; 
};

x v { 1, 2 }; // До C++14 нельзя было опустить третье поле "c"
```


## const expr
TODO все возможные модификаторы переменных, const/volotile etc


## static_assert
## range based for

## multithreading
## chrono
## random