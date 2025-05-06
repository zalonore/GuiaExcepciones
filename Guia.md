# Guía de Estudio: Gestión de Excepciones en C++

## Objetivo de la clase

Al finalizar esta lección, serás capaz de identificar, utilizar y diseñar correctamente bloques de manejo de excepciones
para evitar la terminación inesperada de programas, asegurando así una mayor robustez y mantenibilidad del software.

---

## 1. Motivación: ¿Por qué es importante gestionar bien los errores?

Antes de entrar en la definición técnica, es importante entender **por qué** el manejo adecuado de excepciones no es
solo una buena práctica, sino una necesidad en el desarrollo de software serio. A través de algunos ejemplos reales y
cotidianos, podemos evidenciar cómo una gestión inadecuada de errores puede tener efectos críticos. Esto nos prepara
para comprender el verdadero valor de las excepciones como herramienta profesional.

### 🔥 Casos reales donde falló la gestión de excepciones:

- **NASA - Mars Climate Orbiter (1999):** Una mala gestión de errores (y una confusión de unidades) causó la pérdida de
  una misión de 125 millones de dólares. Si se hubieran validado correctamente los datos y se hubiera lanzado una
  excepción, se podría haber detectado el error a tiempo.

- **Sistema bancario - ATM:** Algunos cajeros automáticos no implementan correctamente excepciones al comunicarse con
  los servidores. Esto ha ocasionado que se retenga la tarjeta o se congele la interfaz, generando frustración y pérdida
  de confianza del usuario.

- **Software educativo:** En plataformas donde los datos de notas o asistencias se ingresan manualmente, no manejar
  correctamente entradas inválidas (como una nota de 7.0 en un sistema sobre 5.0) puede propagar errores graves en
  reportes académicos. Aunque en muchos casos el software no colapse inmediatamente, el daño se refleja más adelante en
  cálculos incorrectos, confusiones administrativas o pérdida de confianza en el sistema.

Estas situaciones nos conducen a un problema más general:

- **Código silencioso:** A veces los errores no se ven porque el código no hace nada cuando debería lanzar una
  excepción. Por ejemplo:
  ```cpp
  if (nota <= 5.0) {
      // hacer
  } // No considera el caso en el que no
  ```
  Este tipo de "errores silenciosos" puede hacer que se almacenen datos inválidos o que los problemas pasen
  desapercibidos hasta mucho después. En estos casos, es mucho mejor lanzar una excepción con un mensaje claro:
  ```cpp
  if (nota <= 5.0){
      // Hacer
  }
  else {
      // Gestionar el error
      throw out_of_range("La nota no puede ser mayor a 5.0");
  }
  ```
  💡 **Tip de calidad:** No incluir un `else` cuando se requiere una validación o asumir que los datos siempre serán
  válidos es una forma común de fallar en el manejo de errores. Este tipo de omisiones crean lo que se conocen como
  *errores silenciosos*: el programa continúa como si todo estuviera bien, pero está procesando datos erróneos. Estos
  errores son especialmente difíciles de detectar y corregir si no se hace un manejo explícito mediante excepciones. ¡No
  ignores lo inesperado, atrápalo con una excepción!

Como puedes ver, estas situaciones ilustran que **no capturar o lanzar adecuadamente las excepciones puede tener
consecuencias graves**, tanto técnicas como económicas y sociales. Aprender a manejarlas correctamente no solo mejora tu
código, sino que también te convierte en un profesional más competente. Esta es una **habilidad fundamental** que ayuda
a construir software más confiable, mantenible y preparado para enfrentar condiciones reales.

---

## 2. ¿Qué es una excepción y por qué usarla?

Una **excepción** es un mecanismo que permite detectar y manejar errores en tiempo de ejecución. En lugar de dejar que
un programa se bloquee o devuelva resultados incorrectos, las excepciones permiten capturar el error, manejarlo y
continuar con la ejecución o salir de forma controlada.

Es importante entender que **no todos los errores pueden o deben resolverse simplemente imprimiendo mensajes en pantalla
**. En muchas arquitecturas bien diseñadas, especialmente en sistemas medianos o grandes, las responsabilidades están
separadas: no todas las clases deben saber cómo comunicarse con el usuario. Una clase encargada de cálculos, validación
o almacenamiento no debería estar preocupada por cómo mostrar un mensaje de error.

💡 **Tip de diseño**:
> Usar excepciones permite delegar la responsabilidad de mostrar el error al usuario a una capa
superior del sistema (como la interfaz gráfica o de consola), sin comprometer la lógica de negocio o los componentes
internos. Esto **respeta el principio de separación de responsabilidades** y contribuye a un diseño más limpio,
reutilizable y mantenible.
---

### 🧩 Ejemplo aplicado con separación de responsabilidades

Supongamos que tenemos una función encargada de validar edades y una función principal que se encarga de interactuar con
el usuario:

```cpp
#include <iostream>
#include <stdexcept>

int validarEdad(int edad) {
    if (edad < 0 || edad > 120) {
        throw std::out_of_range("Edad no válida");
    }
    return edad;
}

int main() {
    int edad;
    std::cout << "Ingrese su edad: ";
    std::cin >> edad;

    try {
        int edadValida = validarEdad(edad);
        std::cout << "Edad válida: " << edadValida << std::endl;
    } catch (std::exception& e) {
        std::cerr << "Error al procesar la edad: " << e.what() << std::endl;
    }

    return 0;
}
```

En este ejemplo, la función `validarEdad` está enfocada solo en la lógica de validación y no imprime nada. La función
`main` es la que se encarga de interactuar con el usuario. Esta separación facilita el mantenimiento del código, permite
reutilizar la función `validarEdad` en otros contextos (como una interfaz gráfica), y promueve un diseño limpio y
profesional.

---

### 🧱 Ejemplo orientado a objetos con separación de archivos

A continuación se presenta un ejemplo con dos clases (`Producto` y `Tienda`), separadas en archivos `.h` y `.cpp`, que
incluye manejo de excepciones y una clara separación de responsabilidades.

#### `Producto.h`

```cpp
#ifndef PRODUCTO_H
#define PRODUCTO_H

#include <string>

class Producto {
private:
    std::string nombre;
    double precio;

public:
    Producto(const std::string& nombre, double precio);
    std::string getNombre() const;
    double getPrecio() const;
};

#endif
```

#### `Producto.cpp`

```cpp
#include "Producto.h"
#include <stdexcept>

Producto::Producto(const std::string& nombre, double precio) : nombre(nombre), precio(precio) {
    if (nombre.empty()) {
        throw std::invalid_argument("El nombre del producto no puede estar vacío.");
    }
    if (precio <= 0) {
        throw std::out_of_range("El precio debe ser mayor que cero.");
    }
}

std::string Producto::getNombre() const {
    return nombre;
}

double Producto::getPrecio() const {
    return precio;
}
```

#### `Tienda.h`

```cpp
#ifndef TIENDA_H
#define TIENDA_H

#include "Producto.h"
#include <vector>

class Tienda {
private:
    std::vector<Producto*> inventario;

public:
    ~Tienda();
    void agregarProducto(Producto* p);
    void listarProductos() const;
};

#endif
```

#### `Tienda.cpp`

```cpp
#include "Tienda.h"
#include <iostream>

Tienda::~Tienda() {
    for (auto p : inventario) {
        delete p;
    }
}

void Tienda::agregarProducto(Producto* p) {
    inventario.push_back(p);
}

void Tienda::listarProductos() const {
    for (const auto& p : inventario) {
        std::cout << "- " << p->getNombre() << ": $" << p->getPrecio() << std::endl;
    }
}
```

#### `main.cpp`

```cpp
#include <iostream>
#include "Tienda.h"

int main() {
    Tienda tienda;

    try {
        Producto* p1 = new Producto("Camiseta", 45000);
        tienda.agregarProducto(p1);

        Producto* p2 = new Producto("Pantalón", -20000);  // Generará excepción
        tienda.agregarProducto(p2);
    } catch (const std::exception& e) {
        std::cerr << "Error al agregar producto: " << e.what() << std::endl;
    }

    std::cout << "Productos en la tienda:";
    tienda.listarProductos();

    return 0;
}
```

Este diseño mantiene una clara separación de responsabilidades:

- La clase `Producto` se encarga de validar su estado.
- `Tienda` solo gestiona una lista de productos.
- `main()` se encarga de la interacción con el usuario.

---

## 3. Sintaxis básica: try, catch, throw
Para implementar correctamente la gestión de excepciones en C++, es importante conocer los tres elementos clave que la componen. Estos permiten estructurar un flujo de control alternativo para manejar condiciones anómalas en tiempo de ejecución:

1. **`throw`**: Se utiliza para **lanzar una excepción** desde una función cuando se detecta una condición inválida que impide continuar con la ejecución normal. Debe ir acompañado de un objeto que describa el error (como un mensaje de texto o una instancia de clase de excepción). Se recomienda lanzar objetos derivados de `std::exception` para una integración consistente con el ecosistema estándar de C++.

2. **`try`**: Delimita un bloque de código donde pueden producirse errores. Su objetivo es **probar** una operación que podría fallar. Si ocurre una excepción dentro del bloque, el control se transfiere inmediatamente al bloque `catch` correspondiente. Es buena práctica envolver solo el código que puede fallar, no todo el programa.

3. **`catch`**: Define una o más rutinas para **capturar y manejar** excepciones específicas. Se puede capturar por tipo (`std::runtime_error`, `std::invalid_argument`, etc.) o de forma genérica (`catch (...)`). Sin embargo, capturar por referencia constante (`const std::exception& e`) es lo más común.

### ¿Cuándo usar try, throw y catch?

Para comprender mejor cómo y cuándo usar cada uno de estos elementos, pensemos en una situación cotidiana:

Imagina que eres el encargado de servir bebidas en una cafetería. Cada vez que alguien te pide una bebida (como preparar una taza de café), debes revisar si tienes todos los ingredientes. Si algo está mal (por ejemplo, no hay café), debes informar el problema a quien pueda resolverlo o al cliente. En programación, esto se refleja así:

- **`throw`** es como levantar la mano y decir: “¡Algo salió mal!” (por ejemplo, no hay café). Se utiliza en el momento preciso en que se detecta un problema.

- **`try`** es como prepararte para servir la bebida sabiendo que *algo podría salir mal*, pero **solo si tienes un plan para reaccionar si eso ocurre**. En C++, el bloque `try` se utiliza únicamente cuando deseas capturar y gestionar una posible excepción justo en ese lugar del código. Por esta razón, siempre debe estar acompañado de uno o más bloques `catch`.

- **`catch`** es lo que haces después: escuchar el problema y tomar una acción, como decirle al cliente que no hay café o redirigirlo a otra bebida. Es decir, *manejas* el error que ocurrió dentro del bloque `try`. Si en ese momento no tienes una forma clara de manejar el error, no necesitas `try`; puedes dejar que la excepción se propague hacia otro lugar del programa donde sí pueda ser gestionada adecuadamente.

En resumen:

- Usa **`throw`** cuando identifiques que no puedes continuar con la ejecución normal por una condición inesperada. Esto es común en funciones de lógica, validación, modelos de datos o servicios auxiliares.

- Usa **`try`** únicamente cuando tienes la intención de capturar y gestionar la excepción **en ese lugar del código**. Lo más común es que los bloques `try-catch` estén ubicados en el `main()` o en las capas que controlan la interacción con el usuario (como una interfaz gráfica o un sistema de menú). Las funciones internas o de lógica no deberían contener `try` salvo que realmente puedan resolver el error allí mismo.

- Usa **`catch`** para manejar los errores que ocurran dentro del bloque `try`, por ejemplo, registrando el problema, mostrando un mensaje de error o tomando decisiones para continuar la ejecución de forma segura. Nunca pongas un `try` sin `catch`, ya que su función es precisamente permitir esa gestión explícita del error.

Esta estructura te permite mantener programas más seguros, legibles y preparados para enfrentar situaciones imprevistas sin colapsar.

### Ejemplo: División por cero

```cpp
#include <iostream>
#include <stdexcept>

int dividir(int a, int b) {
    if (b == 0) {
        throw std::runtime_error("División por cero no permitida");
    }
    return a / b;
}

int main() {
    try {
        // Código que puede lanzar una excepción
        int resultado = dividir(10, 0);
        std::cout << "Resultado: " << resultado << std::endl;
    } catch (std::exception& e) {
       // Código para manejar la excepción
        std::cout << "Error: " << e.what() << std::endl;
    }
}
```

> **what():** es un método que ayuda a obtener un mensaje de error como cadena de caracteres  (const char*)
El método `what()` se encargara de recibir lo que es enviado por domain:error, invalid_argument, out_of_range, etc. Y al ser un string podrá ser usado para mostrarlo al usuario o guardarlo en un log de registro


## 🧠 Consejos prácticos para principiantes

Cuando estás aprendiendo a programar, puede parecer confuso cuándo y cómo usar `throw`, `try` y `catch`. Aquí tienes algunos consejos y ejemplos sencillos para ayudarte:

### 1. 🎯 ¿Cuándo lanzar (`throw`) una excepción?
Lanza una excepción cuando detectes algo que no debería pasar **y no sabes cómo solucionarlo desde la función donde estás**. Por ejemplo:

```cpp
void validarEdad(int edad) {
    if (edad < 0 || edad > 120) {
        throw std::out_of_range("Edad fuera de rango permitido");
    }
}
```

💡 *Piensa en `throw` como una alarma: tu función suena la alarma porque encontró un problema que otra parte del programa debe atender.*

### 2. 🧪 ¿Dónde usar `try` y `catch`?

Usa `try` y `catch` en el lugar donde **sí puedes hacer algo con el error**. Generalmente, esto es en el `main()` o en la parte que interactúa con el usuario.

```cpp
int main() {
    try {
        validarEdad(150);
    } catch (const std::exception& e) {
        std::cout << "Ocurrió un error: " << e.what() << std::endl;
    }
    return 0;
}
```

💡 *Piensa en `try` como un paraguas: estás preparado por si llueve (si ocurre un error), y `catch` es el paraguas que usas para protegerte.*

### 3. 🛠️ Evita estos errores comunes:

- ❌ **Usar `try` en todas partes**: no todo necesita `try`. Solo ponlo donde realmente vas a manejar el error.
- ❌ **`catch (...) {}` sin hacer nada**: no captures un error si no vas a hacer nada con él.
- ❌ **`throw` con tipos primitivos como `int` o `char`**: usa siempre clases de excepción (como `std::runtime_error`).

---

## 🧾 Mini resumen en tabla

| ¿Qué hace? | ¿Cuándo usarlo? | Ejemplo práctico |
|------------|-----------------|------------------|
| `throw`    | Cuando detectas un problema que no puedes resolver en esa función | `throw std::invalid_argument("nombre vacío");` |
| `try`      | Cuando vas a probar código que podría lanzar una excepción | `try { validar(); }` |
| `catch`    | Cuando quieres manejar el error lanzado en el `try` | `catch (const std::exception& e) { ... }` |

## 📚 Clases, métodos y cabeceras estándar de C++ para manejar excepciones

C++ ya viene con una biblioteca estándar que incluye varias clases diseñadas específicamente para ayudarte a lanzar y capturar excepciones de manera ordenada. Aquí te explico algunas de las más importantes, cómo usarlas, y qué librerías debes incluir en tu programa para que funcionen correctamente.

### 🧰 ¿Qué cabeceras debo incluir para usar excepciones?

Para que tu programa reconozca las clases y funciones estándar de manejo de excepciones en C++, necesitas incluir las siguientes cabeceras:

| Cabecera         | ¿Para qué sirve?                                                   |
|------------------|--------------------------------------------------------------------|
| `<exception>`    | Define la clase base `std::exception` y permite heredar de ella.   |
| `<stdexcept>`    | Contiene clases que están basadas en `std::exception` (es decir, que se comportan como una versión especializada de esta clase base) como `runtime_error`, `logic_error`, `invalid_argument`, etc. |
| `<string>`       | (Opcional) Útil para trabajar con mensajes personalizados de error. |
| `<iostream>`     | (Opcional) Necesaria si vas a imprimir errores en consola.         |

Al comienzo de tus programas, puedes escribir algo así:

```cpp
#include <iostream>
#include <stdexcept>
#include <exception>
#include <string>
```

---

### 1. 🧱 `std::exception`
Es la clase base de todas las excepciones estándar. Puedes usarla para capturar cualquier excepción derivada de ella.

```cpp
try {
    throw std::exception();
} catch (const std::exception& e) {
    std::cout << "Se capturó una excepción genérica." << std::endl;
}
```

### 2. 🚨 `std::runtime_error`
Se utiliza para errores que pueden ocurrir durante la ejecución normal de un programa (por ejemplo, un archivo no encontrado).

```cpp
throw std::runtime_error("Archivo no encontrado");
```

### 3. 🎯 `std::logic_error`
Representa errores en la lógica del programa que normalmente deben ser corregidos por el programador (por ejemplo, dividir por cero, acceder a un índice inválido).

Ejemplos de clases derivadas:
- `std::invalid_argument`: argumento no válido
- `std::domain_error`: valor fuera del dominio permitido
- `std::length_error`: violación del tamaño máximo

```cpp
throw std::invalid_argument("El nombre no puede estar vacío");
```

### 4. 🧭 `what()` – Método para obtener información del error
Todas las excepciones estándar tienen un método `what()` que devuelve una descripción del error. Este método es muy útil para mostrar mensajes en pantalla:

```cpp
catch (const std::exception& e) {
    std::cout << "Error: " << e.what() << std::endl;
}
```

### Múltiples catch

En C++, puedes tener **más de un bloque `catch`** para manejar distintos tipos de excepciones de manera específica. Esto es útil cuando quieres dar un trato diferente según el tipo de error.

C++ los revisa en orden, del más específico al más general. Por eso es importante escribir primero los `catch` para tipos más específicos, y al final, si es necesario, un `catch (...)` para capturar cualquier otro error no contemplado.

💡 *Piensa en los `catch` como filtros: cada uno atrapa solo el tipo de error que le corresponde.*

#### Ejemplo con múltiples `catch`:
```cpp
try {
    // código que puede lanzar distints excepciones
    throw std::invalid_argument("Dato no válido");
} catch (const std::invalid_argument& e) {
    std::cout << "Error de argumento: " << e.what() << std::endl;
} catch (const std::runtime_error& e) {
    std::cout << "Error en tiempo de ejecución: " << e.what() << std::endl;
} catch (const std::exception& e) {
    std::cout << "Otro tipo de error: " << e.what() << std::endl;
}
```

---

## 📘 Glosario de términos clave

| Término              | Significado                                                                 |
|----------------------|----------------------------------------------------------------------------|
| Excepción            | Error o situación inesperada que interrumpe el flujo normal del programa. |
| `throw`              | Palabra clave para lanzar una excepción desde una función.                |
| `try`                | Bloque que contiene código que puede fallar y lanzar una excepción.       |
| `catch`              | Bloque que captura y maneja la excepción lanzada en el bloque `try`.      |
| `what()`             | Método que devuelve el mensaje de error de una excepción.                 |
| `std::exception`     | Clase base de todas las excepciones estándar en C++.                      |
| Excepción personalizada | Clase creada por el programador para representar errores específicos.     |
| Capturar una excepción | Significa usar un bloque `try` y `catch` para atrapar un error que ocurrió dentro del `try`. Dentro del `catch` decides qué hacer: mostrar un mensaje, registrar el error o seguir con otra acción.      |
| Propagar una excepción | Significa usar `throw` para lanzar el error y no usar `try-catch` en esa misma función. Así el error será manejado (capturado) en otro lugar del programa, como el `main()`. |

## 🧩 Recuerda

No todas las funciones deben tener `try-catch`. Muchas veces, solo lanzan errores (`throw`) y dejan que sea otro nivel del programa quien los maneje. Esa es una forma muy común y correcta de estructurar tu código. Así, cada parte del programa hace lo que mejor sabe hacer: unas validan, otras deciden qué hacer si algo falla.



## 🧪 Práctica Manejo de excepciones en un sistema de tienda

### 🎯 Objetivo de la actividad

Aplicar el uso de excepciones para robustecer un sistema de tienda, practicando la separación de responsabilidades, la validación de datos, y la propagación de errores con `throw`, `try` y `catch`.

---

### 👨‍💻 Contexto

Deberás crear un sistema básico con las clases `Producto`, `Tienda` y `main()`. La idea es que tengamos un conjunto de productos que puedan ser utilizados en la tienda con algunas interacciones con el usuario. En esta actividad vas dar un siguiente paso en la elaboración de tu código, ahora debes aplicar buenas prácticas de programaicón al utilizar manejo de excepciones y gestionar casos de error.

---

### 🔧 Parte 1 – Creación de la base del sistema

Construye el código base para el funcionamiento de tu tienda:

- Si bien no te debes complicar muhco con la aplicación, si debes asegurarte que puedas gestionar Productos, manipularlos desde la Tienda y comunicarte con el usuario en el Main
- Siempre será buena idea hacer un diseño UML para darte una idea de cómo funcionará tu aplicación.

Mejora a nuestra aplicación:

- Con tu criterio, y basados en lo aprendido en clase. Haz uso de las istrucciones `throw`, `try` y `catch` donde sea necesario
- Asegurate que la comunicación con el usuario sea sólo en la clase `Main`, ello te llevará que gestiones de forma adecuada las excepciones

---

### 🚀 Parte 2 – Detalles del sistema

#### Requerimientos que debe cubrir y donde se espera se usen Excepciones:

1. La tienda no debe aceptar productos con el mismo nombre dos veces.
2. Si se intenta agregar un producto repetido, debe lanzarse una **excepción**.
3. Si se intenta agregar un producto con precio mayor a \$1'000.000, debe lanzar una `std::invalid_argument` con un mensaje apropiado.
4. El método `agregarProducto()` de la Tienda debe validar y propagar las excepciones sin capturarlas (propagación).
5. El `main()` debe tener un bloque `try-catch` que capture y muestre el error usando `what()`.

---

#### 📂 Estructura esperada del proyecto

```
/Tienda
src
├── Producto.h
├── Producto.cpp
├── Tienda.h
├── Tienda.cpp
main.cpp
```

---

### 🧪 Casos que deben probar en `main()`

1. Agregar dos productos con el mismo nombre → debe lanzar una excepción.
2. Agregar un producto con precio negativo o 0 → debe lanzar `std::out_of_range`.
3. Agregar un producto con precio mayor a \$1’000.000 → debe lanzar `std::invalid_argument`.
4. Agregar correctamente un producto y listar todos los productos registrados.

---

## Reflexión individual

- ¿Qué ventajas notaste al usar excepciones en lugar de solo imprimir mensajes?
- ¿Dónde crees que podrías aplicarlo?
- ¿Te pareció más clara la separación entre validar, lanzar y capturar errores?

---

## Lo que se nos viene ...

Antes de cerrar este tema, es útil anticipar cómo se conecta con los próximos temas del curso y que te des la oportunidad de explorarlo por tu cuenta desde ahora:

### 🔍 Debugging
Las excepciones no solo sirven para manejar errores, sino que también son aliadas poderosas al momento de **depurar** tu código. En los próximos encuentros veremos herramientas de debugging que permiten inspeccionar paso a paso cómo se ejecuta tu programa. Cuando usas excepciones bien diseñadas, es más fácil:
- Localizar el punto exacto donde ocurrió el error.
- Ver el mensaje de error que se generó (`what()`).
- Interrumpir la ejecución y analizar el estado del programa en ese momento.
