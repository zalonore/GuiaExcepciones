# Debugging en C++

## 1. ¿Qué es el Debugging?

### Definición y Propósito
El debugging es el proceso de identificar, analizar y corregir errores en un programa. Su propósito es garantizar que el código funcione como se espera, mejorando la calidad y confiabilidad del software.

### Diferencias entre Debugging, Testing y Compilación
| Proceso      | Objetivo                                   | Cuándo se realiza                    |
|--------------|---------------------------------------------|----------------------------------------|
| Compilación  | Convertir el código fuente en ejecutable     | Antes de ejecutar el programa         |
| Testing      | Verificar que el programa cumple requisitos | Antes y después de escribir código     |
| Debugging    | Encontrar y corregir errores                | Durante o después de detectar un error |

### Tipos de Errores
- **Errores sintácticos**: Detectados por el compilador. Ej.: falta de punto y coma.
- **Errores lógicos**: El programa corre, pero no hace lo esperado. Ej.: condición mal formulada.
- **Errores de tiempo de ejecución**: Se producen mientras se ejecuta el programa. Ej.: acceso a puntero nulo.

### 💡 Actividad sugerida
Analiza el siguiente código y clasifica los errores:
```cpp
int main() {
    int a = 5, b;
    int resultado = a / b;
    cout << "Resultado: " << resultado << endl
    return 0;
}
```

---

## 2. El ciclo del Debugging

1. **Reproducción del error**: Implica ejecutar el programa en condiciones controladas para asegurarse de que el error puede ser observado consistentemente. Es fundamental registrar los pasos exactos que llevan al error, incluyendo entradas, configuraciones y contexto del sistema.

2. **Observación**: En esta etapa se presta atención detallada al comportamiento del programa: qué hace, qué no hace y qué resultados inesperados produce. Puede incluir la observación de salidas por consola, mensajes de error o el uso de herramientas como logs.

3. **Formulación de hipótesis**: El programador propone explicaciones posibles sobre la causa del problema, basándose en su conocimiento del código y del sistema. Se puede elaborar una lista de posibles fallos en la lógica, valores incorrectos o interacciones entre componentes.

4. **Verificación**: Se diseñan pequeñas pruebas o se analiza el código paso a paso para confirmar o descartar las hipótesis. Esto puede implicar añadir `cout` o `print` para ver valores internos, o usar el debugger para inspeccionar variables y llamadas.

5. **Corrección**: Una vez que se ha identificado la causa raíz del error, se modifica el código de manera precisa para resolverlo. Es importante evitar cambios innecesarios que puedan introducir nuevos errores.

6. **Validación**: Después de aplicar la corrección, se vuelve a ejecutar el programa para confirmar que el error ha sido eliminado y que el sistema sigue funcionando correctamente. Se recomienda probar también otros casos relacionados para verificar que no haya efectos colaterales.

### 🗨️ Pregunta Guía
“¿Cómo cambia tu forma de pensar cuando haces debugging frente a cuando escribes código por primera vez?”

---

## 3. Herramientas básicas de Debugging (CLion y otros IDEs)

A continuación se presentan las principales herramientas utilizadas en el proceso de depuración dentro de entornos integrados como CLion. Se explican sus funcionalidades, el contexto de uso más adecuado, y se proporciona una analogía cotidiana para facilitar la comprensión de su utilidad.

**Breakpoint (Punto de interrupción)**

Un breakpoint es una marca establecida por el programador en una línea específica del código, que le indica al depurador que debe pausar la ejecución del programa justo antes de ejecutar esa instrucción.

- **Ejemplo cotidiano:** Es como hacer una pausa durante una receta de cocina justo antes de agregar un ingrediente clave, para verificar que tienes la medida correcta y no estás por cometer un error que dañe el plato.
- **Ejemplo técnico:** En un bucle que modifica una variable acumuladora, colocar un breakpoint antes de la operación aritmética permite verificar si la suma se realiza correctamente.
- **¿Para qué sirve?** Permite detener la ejecución del programa en una línea específica del código fuente, con el fin de observar el estado del sistema en ese momento particular.
- **¿Cuándo usarlo?** Es especialmente útil cuando se sospecha que una instrucción en particular está generando un error o un comportamiento inesperado.
- **Beneficio:** Proporciona un acceso directo al contexto del error sin necesidad de agregar impresiones manuales en el código.

**Step Over / Step Into / Step Out**

Estas son funciones de navegación paso a paso que permiten ejecutar el código de forma controlada, observando su comportamiento línea por línea o profundizando en el contenido de funciones.

- **Ejemplo cotidiano:** Es como seguir un camino en una ciudad. Step Over es ver la ciudad desde arriba; Step Into es caminar calle por calle; Step Out es salir rápidamente de un vecindario para regresar al recorrido principal.
- **Ejemplo técnico:** Si un método `calcularPromedio()` retorna un resultado inesperado, ingresar con Step Into permite verificar los valores intermedios utilizados.
- **Step Over:** Ejecuta la línea actual sin entrar en funciones invocadas.
- **Step Into:** Ingresa en una función llamada para ejecutar su contenido paso a paso.
- **Step Out:** Finaliza la función actual y regresa al contexto que la llamó.
- **¿Para qué sirve?** Estas funciones permiten examinar con precisión el flujo de ejecución del programa.
- **¿Cuándo usarlo?** Se recomienda al inspeccionar funciones complejas o cuando el flujo lógico del programa parece no coincidir con el comportamiento observado.
- **Beneficio:** Ayuda a detectar el momento y lugar exacto en que el programa empieza a desviarse del resultado esperado.

**Watch / Evaluate**

Estas herramientas permiten observar en tiempo real el valor de una variable o evaluar expresiones mientras se ejecuta el programa, sin modificar el código fuente.

- **Ejemplo cotidiano:** Es como mirar el velocímetro de un automóvil mientras conduces: te permite observar en todo momento si estás manteniendo la velocidad adecuada o si necesitas ajustar tu ritmo para evitar un exceso que pueda generar problemas.
- **Ejemplo técnico:** Evaluar `totalNotas / cantidadNotas` ayuda a detectar divisiones por cero.
- **¿Para qué sirve?** Permite monitorear el valor actual de una variable o evaluar expresiones en tiempo real durante la ejecución del programa.
- **¿Cuándo usarlo?** Es útil cuando se desea rastrear el comportamiento de una variable específica a lo largo del tiempo.
- **Beneficio:** Facilita la identificación de valores incorrectos sin modificar el código.

**Stack Trace y Frames**

El stack trace es una representación de la pila de llamadas que muestra la secuencia de funciones que ha sido ejecutada hasta el punto actual. Los frames representan cada uno de estos contextos de ejecución.

- **Ejemplo cotidiano:** Es como leer el historial de navegación para ver en qué páginas estuviste antes de llegar a un error en un sitio web.
- **Ejemplo técnico:** Si `leerArchivo()` falla, el stack trace puede mostrar que fue llamada por `cargarDatos()` dentro de `main()`.
- **¿Para qué sirve?** Muestra la secuencia completa de funciones que han sido llamadas hasta el punto actual de ejecución.
- **¿Cuándo usarlo?** Es fundamental cuando se quiere entender cómo se llegó a una función determinada, especialmente en casos de errores profundos.
- **Beneficio:** Facilita el rastreo del origen de errores en aplicaciones con múltiples niveles de llamada.

**Consola de depuración vs consola estándar**

La consola estándar muestra únicamente la salida del programa según las instrucciones del programador (por ejemplo, `cout`). La consola de depuración permite inspeccionar información adicional del estado interno del programa, como valores de variables, ejecución de hilos, advertencias del sistema, entre otros.

- **Ejemplo cotidiano:** Es como ver los ingredientes ocultos en una etiqueta de producto: uno muestra lo visible, el otro te deja ver lo que hay detrás del empaque.
- **Ejemplo técnico:** Mientras que la consola estándar solo muestra "Resultado: 0", la consola de depuración puede revelar que `divisor = 0`.
- **¿Para qué sirve?** La consola de depuración permite inspeccionar el estado interno del programa, mientras que la consola estándar muestra solamente la salida del programa según instrucciones explícitas.
- **¿Cuándo usarlo?** Durante la depuración activa para entender mejor lo que ocurre detrás de la lógica visible.
- **Beneficio:** Proporciona acceso a información más profunda sin tener que modificar el código con salidas de texto adicionales.
- **¿Cómo acceder a ella en CLion?** La consola de depuración se activa ejecutando el programa en modo *Debug*. Esto se hace presionando el botón de "Debug" (ícono de un insecto) ubicado en la esquina superior derecha de la interfaz de CLion. Al hacerlo, se abrirá automáticamente la ventana de depuración en la parte inferior, donde se puede visualizar la consola de depuración, los valores actuales de las variables, y las herramientas de navegación del programa.


## ✏️ Modificación en Tiempo Real de Variables

Una funcionalidad adicional altamente útil que ofrece CLion durante una sesión de depuración es la capacidad de modificar el valor de una variable en tiempo real, sin necesidad de detener la ejecución o recompilar el programa. Esta característica se denomina **Set Value**.

### ¿Para qué sirve?

Permite cambiar el valor de una variable durante una ejecución pausada para observar cómo se comporta el programa con un nuevo valor. Es útil para probar escenarios alternativos sin modificar el código fuente.

### ¿Cómo se utiliza?

Durante una sesión de depuración, en el panel de variables:

1. Haz clic derecho sobre la variable cuyo valor deseas modificar.
2. Selecciona la opción **"Set Value"**.
3. Ingresa el nuevo valor en el cuadro de diálogo que aparece.

### 🧪 Ejemplo aplicado

Si al depurar se observa que la variable `precio` tiene un valor negativo inesperado, se puede cambiar su valor a `50000` temporalmente para ver si el flujo continúa sin lanzar la excepción.

```cpp
// Supongamos que tienes el siguiente código:
Producto* p = new Producto("Zapatos", -100); // Aquí se lanza la excepción

// Durante el debugging puedes cambiar -100 por 50000 directamente en la interfaz.
```
>Precaución: esta funcionalidad no modifica el archivo fuente ni persiste entre ejecuciones. Su uso es exclusivo para análisis temporal en tiempo de depuración y debe emplearse como herramienta de diagnóstico, no como sustituto de la corrección del código.

### Panel Threads & Variables en CLion

Durante una sesión de depuración, CLion proporciona una herramienta esencial denominada **“Threads & Variables”**, que permite observar el estado interno del programa en tiempo real.

#### ¿Qué se puede observar?
- **Threads (hilos)**: Lista todos los hilos de ejecución activos, su estado actual y la función en la que se encuentran. Esto es crucial para programas concurrentes o que utilizan múltiples hilos.
- **Variables**: Muestra las variables locales, globales y estáticas dentro del contexto actual del stack. Permite también observar estructuras más complejas como objetos, vectores y mapas.
- **Expresiones observadas (Watch)**: Se pueden agregar expresiones personalizadas para seguir su evaluación paso a paso.

#### ¿Cómo acceder al panel?
1. Iniciar la depuración del programa mediante el botón **Debug** (ícono de insecto).
2. En la parte inferior del entorno, seleccionar la pestaña **Debugger**.
3. Allí se despliega automáticamente la vista de **Threads & Variables**.

#### ¿Para qué sirve?
- Comprender en qué punto exacto del flujo de ejecución se encuentra cada hilo.
- Diagnosticar bloqueos o condiciones de carrera en programas multihilo.
- Observar cómo cambian los valores de las variables a medida que avanza la ejecución.
- Evaluar el estado de estructuras de datos complejas sin modificar el código.

#### Ejemplo aplicado:
Durante la depuración del método `agregarProducto()`:
- Es posible observar el valor de `p->getNombre()` y `p->getPrecio()`.
- Se puede inspeccionar el contenido del vector `inventario` y verificar si contiene duplicados.
- Si se usan múltiples hilos (por ejemplo, para cargar productos desde archivos), se puede observar cuál hilo lanzó la excepción.

#### Analogía cotidiana:
Es como entrar a una sala de control de una fábrica donde cada línea de producción (hilo) puede ser monitoreada. Puedes ver qué máquina está activa (función), qué producto está procesando (variable), y detener o examinar su funcionamiento si algo parece anómalo.


### Tipos de Breakpoints disponibles en CLion

CLion permite configurar diversos tipos de breakpoints para facilitar la depuración. A continuación se describen los principales disponibles en este entorno:

**1. Breakpoint de línea (line breakpoint)**

- **Ejemplo cotidiano:** Es como poner una señal de alto justo antes de una curva para detenerse y revisar si es seguro continuar.
- **Ejemplo técnico:** Detiene el programa en una línea específica del código para examinar variables locales.
- **Disponible en CLion:** ✅

**2. Breakpoint condicional (conditional breakpoint)**

- **Ejemplo cotidiano:** Es como decir "solo me detendré si el semáforo está en rojo".
- **Ejemplo técnico:** Se activa solo cuando se cumple una condición, por ejemplo: `x == 5`.
- **Disponible en CLion:** ✅ (haciendo clic derecho sobre el breakpoint y seleccionando "Editar" para definir la condición)

**3. Breakpoint de excepción (exception breakpoint)**

- **Ejemplo cotidiano:** Es como encender una alarma cuando se rompe un vaso, sin importar quién lo haya roto.
- **Ejemplo técnico:** Detiene el programa cuando ocurre una excepción, como acceso ilegal a memoria o división por cero.
- **Disponible en CLion:** ✅ (desde el panel de breakpoints → botón "+" → "C++ Exception Breakpoint")


### 🔧 Mini Práctica

Abre un proyecto en CLion con este código:

```cpp
int dividir(int a, int b) {
    return a / b;
}

int main() {
    int resultado = dividir(10, 0);
    cout << "Resultado: " << resultado << endl;
    return 0;
}
```

- Coloca un breakpoint en `return a / b`.
- Usa Step Into y observa el valor de `b`.
- Corrige el error para evitar la división por cero.

## 🧪 Actividades prácticas de debugging a partir del sistema de Tienda

### 🎯 Objetivo general de los ejercicios

Los estudiantes deberán ser capaces de:

- Identificar errores de ejecución que se derivan de excepciones no capturadas en tiempo de ejecución.
- Emplear de manera adecuada los puntos de interrupción (breakpoints) de línea y los breakpoints de excepción disponibles en el entorno CLion.
- Aplicar técnicas de depuración como el seguimiento detallado del flujo de ejecución mediante `Step Into`, el uso de `Evaluate Expression` y el análisis del stack trace para localizar y comprender errores.
- Fortalecer la comprensión de la relación entre el diseño orientado a objetos, la gestión estructurada de excepciones y el proceso de depuración sistemática del software.

### 🧩 Ejercicio 1: Diagnóstico de un fallo por excepción

**Objetivo:** Detectar el lugar exacto donde se produce una excepción en tiempo de ejecución (validación de datos).

1. Comentar el bloque `try-catch` del `main.cpp` de la tienda.
2. Ejecutar el programa en modo Debug.
3. Usar un *exception breakpoint* para detener la ejecución automáticamente cuando se lance una excepción.
4. Utilizar el *stack trace* para identificar desde qué parte del código se propagó el error.
5. Utilizar *Evaluate Expression* para analizar el valor de las variables implicadas en el error.

**Pregunta reflexiva:**

- ¿Dónde ocurrió exactamente el fallo?
- ¿Qué variable tenía un valor inesperado?
- ¿Cómo usarías este dato para mejorar la validación?

### 🔄 Ejercicio 2: Seguimiento de flujo con Step Into

**Objetivo:** Explorar cómo se propaga una excepción desde la clase `Producto` hasta el `main()`.

1. Colocar un **breakpoint de línea** al inicio del constructor de `Producto`.
2. Ejecutar en modo Debug.
3. Utilizar **Step Into** para ingresar en cada función hasta llegar al lugar donde se lanza la excepción.
4. Observar cómo se propaga el error hacia arriba usando el stack trace.
5. Usar **Watch** para observar el valor de `precio` y verificar la condición que activa el `throw`.

### 🔁 Ejercicio 3: Errores silenciosos y uso de condicionales

**Objetivo:** Identificar errores lógicos que no generan excepciones pero causan mal comportamiento.

1. Quitar temporalmente las validaciones dentro del constructor de `Producto` (ni `throw`, ni validaciones).
2. Agregar un producto con nombre vacío y precio negativo.
3. Colocar **breakpoints condicionales** para que el programa se detenga si `nombre == ""` o `precio <= 0`.
4. Evaluar el impacto de este error en la lógica de la tienda.

### 🧱 Ejercicio 4: Validar duplicados con breakpoint condicional

**Objetivo:** Detectar si se están agregando productos duplicados sin control.

1. Implementar la lógica de control de duplicados en `agregarProducto` usando un bucle que compare los nombres.
2. Colocar un **breakpoint condicional** en la línea de inserción `push_back`, activado solo si `p->getNombre() == "Camiseta"`.
3. Probar agregar dos productos con ese nombre y depurar la ejecución.

### 🧠 Reflexión final

- ¿Qué tipo de breakpoint te resultó más útil para detectar errores lógicos?
- ¿Qué diferencias notaste entre depurar con `cout` y con herramientas visuales?
- ¿Qué aprendiste sobre cómo se relaciona la validación de datos con el diseño orientado a objetos?

---



## Documentación adicional recomendada
* https://www.jetbrains.com/help/clion/debugging-code.html  y todas las páginas relacionadas con debugging
