# Informe: elementos del desarrollo de un programa informático

## Parte 1. Conceptos

### 1.1 Código fuente, código objeto y código ejecutable

**Código fuente.** Es el programa tal como lo escribe el programador en un lenguaje como C, Java o Python. Es texto que una persona puede leer, pero el ordenador todavía no lo entiende: hay que traducirlo antes.

**Código objeto.** Es lo que sale cuando el compilador traduce el código fuente a lenguaje máquina (ceros y unos). Aún no funciona por sí solo, porque le faltan piezas que están en otros archivos o bibliotecas.

**Código ejecutable.** Es el programa ya terminado. Se crea cuando el enlazador junta el código objeto con esas piezas que le faltaban. Este ya se puede abrir y usar.

Ejemplo con un programa en C llamado `programa.c`:

```c
#include <stdio.h>

int main(void) {
    int precio = 10;
    int total = precio * 2 + 3 * 4;
    printf("Total: %d\n", total);
    return 0;
}
```

Con el compilador GCC se pasa por los tres tipos de código:

```bash
gcc -c programa.c -o programa.o   # código fuente -> código objeto
gcc programa.o -o programa.exe    # código objeto -> código ejecutable
./programa.exe                    # lo ejecuta y muestra "Total: 32"
```

#### Fases desde que se escribe hasta que se ejecuta

1. **Escribir el código.** El programador crea el archivo `programa.c`.
2. **Compilar.** El compilador lo traduce y crea el código objeto (`programa.o`).
3. **Enlazar.** El enlazador une el código objeto con las bibliotecas y crea el ejecutable (`programa.exe`).
4. **Cargar.** Al abrirlo, el sistema operativo lo mete en la memoria RAM.
5. **Ejecutar.** El procesador va leyendo y haciendo las instrucciones una a una.

La parte de compilar se divide en seis fases:

1. **Análisis léxico.** Lee el código y lo parte en piezas pequeñas (palabras, números, signos). Si hay un símbolo raro que no existe en el lenguaje, da error.
2. **Análisis sintáctico.** Comprueba que esas piezas están bien ordenadas según las reglas del lenguaje. Si falta algo (por ejemplo un `;`), da error.
3. **Análisis semántico.** Comprueba que tiene sentido: que las variables existan, que los tipos encajen, etc.
4. **Código intermedio.** Traduce el programa a una versión más simple, con una operación por línea.
5. **Optimización.** Mejora ese código para que sea más rápido o más corto, sin cambiar lo que hace. Por ejemplo, `3 * 4` lo calcula ya y lo deja en 12.
6. **Código final.** Traduce todo a instrucciones del procesador y lo guarda en el archivo objeto.

Resumen de dónde aparece cada uno:

| Tipo de código | Cuándo aparece | Ejemplo |
|---|---|---|
| Fuente | Es lo que escribimos, la entrada del compilador | `programa.c` |
| Objeto | Lo que sale del compilador | `programa.o` |
| Ejecutable | Lo que crea el enlazador y ya se puede usar | `programa.exe` |

### 1.2 Clasificación de los lenguajes

#### Por nivel

El nivel indica cuánto se parece el lenguaje al idioma de las personas o al del ordenador.

**Bajo nivel.** Muy cerca del procesador. Difíciles de entender.
- Lenguaje máquina: puros ceros y unos.
- Ensamblador: abreviaturas en vez de binario, por ejemplo `mov eax, 5`.

**Nivel medio.** Tienen cosas fáciles de usar, pero también dejan tocar la memoria directamente.
- C
- C++

**Alto nivel.** Fáciles de leer, parecidos al inglés, y funcionan en cualquier ordenador.
- Python
- Java

| Nivel | Idea | Ejemplos |
|---|---|---|
| Bajo | Habla directamente con el procesador | Lenguaje máquina, ensamblador |
| Medio | Fácil de usar pero toca la memoria | C, C++ |
| Alto | Parecido al lenguaje humano | Python, Java |

#### Por paradigma

El paradigma es la forma de resolver el problema.

**Imperativo.** Dices **cómo** hacerlo, paso a paso.
- C
- Java

**Declarativo.** Dices **qué** quieres y el propio lenguaje se encarga de conseguirlo.
- SQL
- Haskell

| Paradigma | Idea | Ejemplos |
|---|---|---|
| Imperativo | Dices cómo hacerlo | C, C++, Java, Python |
| Declarativo | Dices qué quieres | SQL, Haskell, Prolog |

## Parte 2. Práctica

### 2.1 Los cuatro fragmentos

Para saber cuál es cada uno uso esta regla: si dice **cómo** hacerlo paso a paso, es imperativo; si dice **qué** quiere, es declarativo.

**Fragmento 1: sumar una lista de números uno a uno.**
Es **imperativo**. Va sumando número a número y guardando el total en cada paso.

```python
total = 0
for numero in numeros:
    total = total + numero
```

**Fragmento 2: nombres de los empleados mayores de 30 años.**
Es **declarativo**. Solo pide qué datos quiere (los nombres) y la condición (edad mayor de 30). No dice cómo buscarlos.

```sql
SELECT nombre
FROM empleados
WHERE edad > 30;
```

**Fragmento 3: factorial recursivo.**
Es **declarativo**. No da pasos: define qué es el factorial con dos reglas, como en matemáticas.

```haskell
factorial 0 = 1
factorial n = n * factorial (n - 1)
```

**Fragmento 4: filtrar los productos de más de 10 dólares uno a uno.**
Es **imperativo**. Recorre la lista, mira cada producto y guarda los que valen más de 10.

```python
caros = []
for producto in productos:
    if producto.precio > 10:
        caros.append(producto)
```

Resumen:

| Fragmento | Paradigma | Por qué |
|---|---|---|
| 1 | Imperativo | Suma paso a paso |
| 2 | Declarativo | Solo pide los datos |
| 3 | Declarativo | Define el factorial con reglas |
| 4 | Imperativo | Recorre la lista él mismo |

### 2.2 Actividad del día a día

**Actividad elegida:** poner una lavadora.

**Forma imperativa (paso a paso):**

1. Separo la ropa blanca de la de color.
2. Vacío los bolsillos y miro las etiquetas.
3. Meto la ropa en la lavadora sin llenarla del todo.
4. Echo el detergente y el suavizante.
5. Cierro la puerta.
6. Elijo el programa de algodón a 40 grados.
7. Le doy al botón de inicio.
8. Cuando acaba, tiendo la ropa.

**Forma declarativa (qué quiero):**

> Quiero la ropa limpia y seca, sin que se estropee.

**Comparación:**

| | Imperativo | Declarativo |
|---|---|---|
| Qué digo | Cómo hacerlo | Qué quiero |
| Largo | Muchos pasos | Una frase |
| Control | Todo lo controlo yo | Lo decide quien lo haga |

**Ventajas y desventajas.**
Con el imperativo controlo todo, pero es más largo y tengo que saber cómo se hace. Con el declarativo es más corto y fácil, pero no controlo los detalles y dependo de que otro sepa hacerlo.

**Conclusión.** El imperativo va bien cuando quiero controlar cómo se hace algo, y el declarativo cuando solo me importa el resultado. Muchas veces se juntan: al elegir el programa de la lavadora soy declarativo (digo qué lavado quiero), pero por dentro la lavadora sigue unos pasos que alguien programó de forma imperativa.

## Palabra del día

Compañeros
