# Особенности языка

## const

### Переменные

```cpp

//Константный int
const int i = 1; 

//Альтернативная запись
int const j = 2; 

//Указатель на константный int
const int* pI = &i; 

//Константный указатель
int const* const cP = &i; 

//Снятие модификатора
const_cast<int&>(i) = 3; 

int k = 0;
// Ошибка компиляции, так можно добавить константность
static_cast<const int&>(k) = 1; 
```

### Функции

```cpp
class A
{
    std::string value = "test";
    int i = 0;
    
    mutable int j = 0;

    int* p_i; //Предположим что оно проиницилизированно

public:

    void f(const int i) {}

    //Ошибка компиляции, сигнатура считается одинаковой
    void f(int) {} 

    //Так функции могут быть перегружены
    void f_ref(int& r_i) {} 

    //Данная функция будет вызванна от r-value или const int как аргумента
    void f_ref(const int& c_r_i) {}

    //Возврат константной ссылки
    const string& get_const_ref() { return value; } 

    //const функция не даст изменить состояние объекта
    //Из неё могут вызываться только другие const функции
    int const_f() const { return i; } 
    
    //const функции можно перегружать, и они будут выбираться в зависимости от нашего объекта типа A
    int const_f() { return i };

    //Модификация j разрешена из-за ключевого слова mutable
    void set_const_f() const { j = 1; } 
    

    //Такой вызов разрешён, т.к. сам указатель не меняется
    void update_value() const { *p_i = 0; }
}
```