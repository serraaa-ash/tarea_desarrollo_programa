# Informe: elementos del desarrollo de un programa informático

## Parte 1. Análisis teórico de conceptos

### 1.1 Código fuente, código objeto y código ejecutable

#### Qué es cada uno

**Código fuente.** Es el programa tal y como lo escribe el programador en un lenguaje de programación (C, Java, Python...). Es texto que una persona puede leer y modificar, con variables, condiciones, bucles o funciones, pero el procesador no lo entiende directamente: antes hay que traducirlo.

**Código objeto.** Es lo que se obtiene cuando el compilador traduce el código fuente a código máquina, es decir, a instrucciones binarias del procesador. Todavía no es un programa completo: contiene llamadas a funciones que están en otros archivos o en bibliotecas (por ejemplo, `printf`) y que aún no se han unido, así que no se puede ejecutar.

**Código ejecutable.** Es el programa final. Lo genera el enlazador (*linker*) uniendo uno o varios archivos de código objeto con las bibliotecas que necesitan. El sistema operativo ya puede cargarlo en memoria y el procesador ejecutarlo.

Como ejemplo usaré este programa en C, guardado en `programa.c`:

```c
#include <stdio.h>

int main(void) {
    int precio = 10;
    int total = precio * 2 + 3 * 4;
    printf("Total: %d\n", total);
    return 0;
}
```

Con el compilador GCC se obtienen los tres tipos de código:

```bash
gcc -c programa.c -o programa.o   # compila: código fuente -> código objeto
gcc programa.o -o programa.exe    # enlaza: código objeto -> código ejecutable
./programa.exe                    # ejecuta y muestra "Total: 32"
```

#### Fases desde que se escribe el programa hasta que se ejecuta

1. **Edición.** El programador escribe el código fuente (`programa.c`).
2. **Compilación.** El compilador traduce el código fuente en seis fases, explicadas debajo, y genera el código objeto (`programa.o`).
3. **Enlazado.** El enlazador une el código objeto con las bibliotecas necesarias y genera el ejecutable (`programa.exe`).
4. **Carga.** Al lanzar el programa, el sistema operativo lo copia en la memoria RAM y le reserva los recursos que necesita.
5. **Ejecución.** El procesador lee, decodifica y ejecuta las instrucciones una tras otra.

Las seis fases de la compilación forman dos bloques. Las tres primeras son de **análisis**: estudian el código fuente para entenderlo y detectar errores. Las tres últimas son de **síntesis**: construyen el código objeto.

**1. Análisis léxico.** Lee el código fuente carácter a carácter y lo agrupa en *tokens*, que son las «palabras» del lenguaje. La línea `int total = precio * 2 + 3 * 4;` se divide así:

| Token | Tipo |
|---|---|
| `int` | Palabra reservada |
| `total`, `precio` | Identificadores |
| `=` | Operador de asignación |
| `*`, `+` | Operadores aritméticos |
| `2`, `3`, `4` | Números (literales) |
| `;` | Fin de instrucción |

Si aparece un símbolo que no pertenece al lenguaje, como la `@` en `int total = precio @ 2;`, se produce un **error léxico**.

**2. Análisis sintáctico.** Comprueba que los tokens están ordenados según las reglas (la gramática) del lenguaje y construye con ellos un árbol sintáctico. En el árbol de nuestra línea, las multiplicaciones quedan por debajo de la suma porque se calculan antes:

```text
          =
        /   \
    total     +
            /   \
          *       *
         / \     / \
    precio  2   3   4
```

Si falta algo que la gramática exige, como en `int total = precio * ;` (falta un operando), se produce un **error sintáctico**.

**3. Análisis semántico.** Comprueba que lo que está bien escrito también tiene sentido: que las variables estén declaradas, que los tipos de datos sean compatibles o que las funciones reciban los argumentos correctos. Por ejemplo, `int total = precio * cantidad;` es sintácticamente correcta, pero si `cantidad` no se ha declarado en ninguna parte, se produce un **error semántico**.

**4. Generación de código intermedio.** El compilador traduce el árbol a una representación sencilla, con una operación por línea, que todavía no depende de ningún procesador concreto. Un formato habitual es el código de tres direcciones:

```text
t1 = precio * 2
t2 = 3 * 4
t3 = t1 + t2
total = t3
```

**5. Optimización.** Mejora el código intermedio para que el programa sea más rápido o más pequeño, sin cambiar lo que hace. Aquí, `3 * 4` siempre vale 12, así que se calcula ya al compilar, y sobran variables temporales:

```text
t1 = precio * 2
total = t1 + 12
```

Un compilador que optimice todavía más vería que `precio` siempre vale 10 y guardaría directamente `total = 32`.

**6. Generación de código final.** Traduce el código optimizado a instrucciones del procesador concreto (por ejemplo, un procesador x86) y las guarda en binario en el archivo objeto `programa.o`. Escritas en ensamblador para que se puedan leer, serían más o menos así:

```asm
mov   eax, [precio]    ; carga precio en un registro
imul  eax, eax, 2      ; lo multiplica por 2
add   eax, 12          ; le suma 12
mov   [total], eax     ; guarda el resultado en total
```

#### Dónde interviene cada tipo de código

| Tipo de código | Cuándo interviene | Ejemplo |
|---|---|---|
| Código fuente | Es la entrada de la compilación: lo lee el análisis léxico y lo revisan el sintáctico y el semántico | `programa.c` |
| Código objeto | Es la salida de la generación de código final, después del código intermedio y la optimización | `programa.o` |
| Código ejecutable | Lo produce el enlazador; después se carga en memoria y lo ejecuta el procesador | `programa.exe` |

> **Nota:** no todos los lenguajes siguen exactamente este camino. Java compila a *bytecode* (archivos `.class`), un código intermedio que ejecuta la máquina virtual de Java (JVM). Python usa un intérprete que va traduciendo y ejecutando el programa sobre la marcha, sin generar un `.exe`. En todos los casos, el código fuente acaba convertido en instrucciones que entiende el procesador.