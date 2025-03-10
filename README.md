# Vectores-

## Implementación de la clase vector

```c++


#include <iostream>
#include <cassert>
#include <initializer_list>

using namespace std;

template <typename T>

class Vector{
private: 
    T* storage_;
    unsigned int capacity_;
    unsigned int size_;

public: 
Vector() { //Constructor por defecto
    capacity_ = 100;
    storage_ = new T[capacity_];
    size_ = 0;
}

Vector(unsigned int c, const T elem ) { //Constructor para añadir un elemento cierta cantidad de veces
    capacity_ = c;
    storage_ = new T[capacity_];
    for(unsigned int i= 0; i < c; i++) {
        storage_[i] = elem;
    }
    size_ = capacity_;
}

Vector(std::initializer_list<T> init){ //Constructor para aceptar datos de tipo {1, 2, 3}
size_ = init.size();
capacity_ = size_;
storage_ = new T[capacity_];
std::copy(init.begin(), init.end(), storage_);

}


unsigned int size() { //Metodo para conocer el tamaño del vector
    return size_;
}

unsigned int capacity() {  
    return capacity_;
}

private:

void resize() { //Metodo privado para incrementar la capacidad del vector
    unsigned int capacity2_ = capacity_ * 1.5;
    T* storage2_ = new T[capacity2_];

    for(unsigned int i = 0; i < size_; i++) {
        storage2_[i] = storage_[i];
    }

    delete[] storage_; 
    storage_ = storage2_;
    capacity_ = capacity2_;
}

public:

const T& at(unsigned int index) const { //Metodo para devolver el dato guardado en index

assert(index >= 0 && index < size_);
return storage_[index];
}

void push_back(const T& elem) { //Metodo para añadir un elemento al final del vector

    if(size_ == capacity_) {
        resize();
    }

    storage_[size_] = elem;
    size_++;
}

void pop_back() { //Metodo para eliminar el ultimo elemento
    size_--;
}

void push_front(const T& elem) { //Metodo para añadir un elemento al inicio del vector
    if(size_ == capacity_) {
        resize();    
    }
    
    for(unsigned int i=size_ ; i>0 ; i--) {
        storage_[i] = storage_[i-1];
    }
    storage_[0] = elem;
    size_++;
}

void pop_front() { //Metodo para eliminar el primer elemento del vector

    for(unsigned int i=0; i<(size_ - 1) ; i++  ) {
        storage_[i] = storage_[i+1];
    }   
    size_--;
}

unsigned int waste() { //Metodo para conocer el desperdicio del vector
    return capacity_ - size_;
}

bool empty() const { //Metodo para saber si el vector esta vacio
    return size_ == 0;
}

void insert(const T& elem, unsigned int index ) { //Metodo para insertar un elemento en la posicion index
    assert(index >= 0 && index < size_ );
    if(size_== capacity_) {
     resize(); 
    }

    for(unsigned int i = size_; i>index; i--) {
        storage_[i] = storage_[i-1];
    }
    storage_[index] = elem;
    size_++;
}

void erase(unsigned int index) { //Metodo para eliminar el elemento de la posicion index
    assert( index >= 0 && index < size_);
    for(unsigned int i = index; i<(size_ - 1) ; i++ ) {
        storage_[i] = storage_[i+1];
    }
    size_--;
}


void print() { //Metodo para imprimir un vector

    cout << "{";
    for(unsigned int i = 0; i<size_ ; i++ ) {
        cout << " " << storage_[i];
    }
    cout << "}" << endl;
}

};

```
## Remover Numeros duplicados 
```c++

bool seen(Vector<int> &vec, int elem) { //Funcion que nos dice si un un elemento esta dentro del vector


for(int i=0; i<vec.size(); i++) {
    if(vec.at(i) == elem) return true;
}

return false;

}

Vector<int> removeDuplicates(Vector<int> &vec) { //FUncion que remueve los numeros duplicados

    Vector<int> remove;
    Vector<int> duplicate;

    for(int i=0; i < vec.size(); i++) {

        if (not seen(duplicate, vec.at(i))) {
            remove.push_back(vec.at(i));    
            duplicate.push_back(vec.at(i));
        }
    }

    return remove;

}

```

### Ejemplo:

```c++

int main() {

Vector<int> numbers = {1, 2, 2, 3, 4, 4, 5};
Vector<int> uniqueNumbers = removeDuplicates(numbers);

uniqueNumbers.print(); // Esperado: {1, 2, 3, 4, 5}

Vector<int> numbers2 = {1,1,1,1,1,1};
Vector<int> uniqueNumbers2 = removeDuplicates(numbers2);
uniqueNumbers2.print(); // Esperado: {1}

Vector<int> numbers3 = {};
Vector<int> uniqueNumbers3 = removeDuplicates(numbers3);
uniqueNumbers3.print(); // Esperado: {}

Vector<int> numbers4 = {1};
Vector<int> uniqueNumbers4 = removeDuplicates(numbers4);
uniqueNumbers4.print(); // Esperado: {1}

return 0;
}
```


~~~
{ 1 2 3 4 5}
{ 1}
{}
{ 1}
~~~


## Analisis de crecimiento de la capacidad interna de un vector

Esta es una funcion que nos imprime 100 veces el tamaño y la capacidad de un vector despues de haberle insertado 100 datos aleatorios. 

```c++
#include <random>
#include <chrono>

using namespace std;
using namespace std::chrono;

void dinamic() {
Vector<int> myvector;
unsigned int size_, capacity_;

for(int i=0; i<100; i++ ) {

    for(int j=0; j<1000; j++) { 
        random_device rd;   
        mt19937 gen(rd());  
        uniform_int_distribution<> dist(0, 100); 
        myvector.push_back(dist(gen));
    }   
     
    size_ = myvector.size();
    capacity_ = myvector.capacity();
    cout << i+1 << " Size: " << size_ << " Capacity: " << capacity_ << endl;
}
    
}
```

Vamos a tomar 100 datos de la capacidad en cada 1000 inserciones. Capacidad va a tener distintas politicas de crecimiento:

- Capacidad + 1
- Capacidad +2
- Capacidad * 1.5
- Capacidad * 2

Estas se van a cambiar cada vez que se haga un resize. Para eso se cambia en el metodo resize de la clase vector

```c++
void resize() { //Metodo privado para incrementar la capacidad del vector
    //unsigned int capacity2_ = capacity_ * 2;
    //unsigned int capacity2_ = capacity_ + 1;
    //unsigned int capacity2_ = capacity_ + 2;
    unsigned int capacity2_ = capacity_ * 1.5;
    //Se va variando cual va a ser la nueva capacidad del vector
    T* storage2_ = new T[capacity2_];

    for(unsigned int i = 0; i < size_; i++) {
        storage2_[i] = storage_[i];
    }

    delete[] storage_; 
    storage_ = storage2_;
    capacity_ = capacity2_;
}

```

Para ver el analisis de este crecimiento, ingrese por favor al siguiente enlace: https://colab.research.google.com/drive/19IaGiGfob2l3HAJ8AA5KBDrP68NJWwY4?usp=sharing





