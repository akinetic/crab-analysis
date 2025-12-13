# 🦀 DST: Algoritmo Universal de Solución Determinística (Análisis Cangrejo)

## 🎯 Introducción y Fundamentos

El **Algoritmo de Solución Determinística (DST)** es un marco analítico universal, diseñado para resolver completamente cualquier juego de suma cero, de información perfecta y de turnos alternos. Este método, conocido como **Análisis Cangrejo** (Inducción Hacia Atrás), etiqueta cada estado del juego con una solución forzada: **Victoria (G_n), Derrota (P_x), o Tablas (T_z)**.

El objetivo del algoritmo no es solo encontrar la solución, sino también determinar el **camino de máxima resistencia** que debe anticipar el jugador para garantizar la victoria o minimizar la derrota.

---

## 🌐 El Juego Universal de los Dos Piratas (Modelo Canónico)

El DST demuestra su universalidad al reducir cualquier juego de 2 jugadores (Ajedrez, Damas, Cruzados) a un modelo canónico abstracto: **El Juego de los Dos Piratas**.

Este juego es un **Grafo de Estados Partisano y Dirigido** donde:

* Las **Islas** son los Estados posibles del juego.
* Los **Piratas** son los jugadores (Blanco y Negro).
* Los **Puentes/Mares** son las transiciones/movimientos permitidos (Puentes).
* El **Cofre** es el Estado que se "roba" y se transporta de isla en isla.

La solución del DST es la **propagación de la etiqueta** del cofre (G_n, P_x, T_z) a través de este mapa hasta la posición inicial. Los estados terminales (G_1) son las islas sin puentes de salida.

---

## 1. Estructura y Notación

### A. La Isla (Estado Único)
Una **Isla** es un nodo único en el grafo de juego, representando una configuración de tablero y el turno. Su clave se define para mantener la consistencia del turno y manejar ciclos:

> Clave Única = Serialización del Tablero + Letra del Jugador cuyo **Turno es Ahora**

Esto implica que el color de la Isla es determinado por la letra del jugador que tiene el turno.

---

## 2. Propagación G_n/P_x (El Bucle de Inducción)

El algoritmo se resuelve desde los estados terminales hacia atrás (G_1). La **Acomodación Global** (actualización de la Base de Tablas) es crucial después de cada asignación G_n o P_x.

| Asignación | Condición de Sucesores | Regla de Índice (Índice en el Antecesor) | Lógica (Máxima Resistencia) |
| :--- | :--- | :--- | :--- |
| **Pérdida (P_x)** | **TODOS** los sucesores son **G_n**. | **x = G_minima + 1** | El oponente (ganador) elegirá la ruta G_n **MÁS BAJA** para finalizar la partida lo más rápido posible. |
| **Victoria (G_n)** | **TODOS** los sucesores son **P_x**. | **n = P_maxima + 1** | El jugador debe anticipar que el oponente (perdedor) elegirá la ruta P_x **MÁS ALTA** para maximizar la duración del juego. |

---
## 3. Asignación de T_z (Tablas)

La etiqueta **T_z** se asigna a las Islas que quedan sin resolver después de que el bucle G_n/P_x se ha detenido (Regla de Exclusión).

### A. Indexación de z (Información Estratégica)

El índice z proporciona un valor informativo sobre la duración máxima de la trampa o la duración mínima del ciclo de tablas.

| Escenario | Sucesores Incluyen | Regla de Índice (Estratégica) | Lógica (Objetivo: Forzar Error Profundo) |
| :--- | :--- | :--- | :--- |
| **Riesgo/Trampa** | Al menos una P_x | **z = P_maxima** | Indica la ruta que contiene el riesgo de pérdida **MÁS PROFUNDO**. La ruta más larga es la que el oponente fallará en calcular (Profundidad del Error). |
| **Ciclo Puro** | SOLO T | **z = T_minima** | Representa el camino más corto para establecer las tablas. |

### B. Estrategia Avanzada: Suma Heurística Ponderada (Z)

Como alternativa estratégica para modelar el potencial total de error del oponente (Densidad de Riesgo), el índice T_z puede ser reemplazado por un puntaje Heurístico **Z**.

El valor Z se calcula como una suma lineal, donde el **valor Z más alto** es la jugada óptima:

$$Z = (N_T \cdot A) + (N_P \cdot B) + \sum_{i=1}^{N_P} (x_i \cdot C)$$

**Racionalidad Estratégica:** Esta es la jerarquía para favorecer la **profundidad de las trampas** sobre la cantidad de opciones. El objetivo es que la cantidad de P sea más importante que la cantidad de T (B > A):

**Prioridad de Pesos:** **C** > **B** > **A**

---

## 4. La Jerarquía de Prioridades (Movimiento Óptimo)

La decisión del jugador al momento de moverse se basa en una estricta jerarquía de prioridades que define el juego óptimo:

1.  **Prioridad Máxima: Ganar (G)**
    * **Acción:** Elegir la **G** con el valor n **MÁS BAJO** (ganar más rápido).
2.  **Prioridad Intermedia: Tablas (T)**
    * **Acción:** Elegir la **T** con el valor z **MÁS ALTO** (máxima resistencia).
3.  **Prioridad Mínima: Perder (P)**
    * **Acción:** Elegir la **P** con el valor x **MÁS ALTO** (minimizar la velocidad de la derrota).

---

## 5. Pseudocódigo Universal del DST

Este es el algoritmo central que implementa la teoría anterior. 

```pseudocode
// ----------------------------------------------------------------------
// ESTRUCTURAS GLOBALES
// ----------------------------------------------------------------------
BASE_DE_TABLAS = Diccionario<Clave_Isla, Solucion> // Almacena (1N : G3, 2B : P4, etc.)
CONTEO_FOTOS = 0 // Contador progresivo para ID único de la 'Foto'

// ----------------------------------------------------------------------
// FUNCIONES DEL DST
// ----------------------------------------------------------------------

FUNCION Resolver_DST(Estado_Inicial)
    // 1. Construcción del Grafo y Asignación de IDs
    Expandir_Grafo(Estado_Inicial)

    // 2. Solución de Victoria/Derrota (G_n y P_x)
    Bucle_Gn_Px()

    // 3. Solución de Tablas (T_z)
    Bucle_Tz()

    RETORNA BASE_DE_TABLAS[Estado_Inicial]
FIN FUNCION

// Construye la estructura de la Isla y sus Puentes (utiliza la función game-specific de Generar_Puentes_de_Salida)
FUNCION Expandir_Grafo(Serializacion_Actual)
    // ... Lógica para asignar el ID progresivo y la Clave Única ...
    // ... Lógica recursiva para explorar todos los Puentes y construir el GRAFO_INVERSO ...
FIN FUNCION

// Aplica las reglas G_n/P_x (P_max + 1, G_min + 1) de forma iterativa y aplica Acomodación Global.
FUNCION Bucle_Gn_Px()
    MIENTRAS Haya_Cambios_En_BASE_DE_TABLAS
        // ... Lógica de Propagación G_n y P_x ...
        // ... Si se asigna, aplicar Acomodación (actualizar antecesores) ...
    FIN MIENTRAS
FIN FUNCION

// Resuelve las T_z usando la Regla de Exclusión y el índice z (P_minima o T_minima).
FUNCION Bucle_Tz()
    MIENTRAS Haya_Cambios_En_Tz_Asignados
        // ... Lógica de Propagación T_z y asignación del índice z ...
    FIN MIENTRAS
FIN FUNCION

```

## 6. Implementación Pragmática y Horizonte Limitado

Para juegos con grafos masivos (como el Ajedrez), el DST se aplica utilizando una **Búsqueda de Horizonte Limitado** (profundidad D). Cuando la búsqueda finaliza en la profundidad D, el algoritmo confía en la **heurística interna T_z** para resolver los nodos no clasificados.

### A. La Regla de Desempate T_z

Cuando el horizonte de búsqueda (profundidad D) no es suficiente para encontrar un **G_n** o **P_x** absoluto, se aplica una regla pragmática de desempate utilizando la **Heurística Estratégica Z** (de la Sección 3B).

1.  **Estados No Resueltos:** Todos los estados no resueltos en el horizonte se asumen como **T_z** (Tablas).
2.  **Acción:** El jugador elige el movimiento que conduce al estado **T_z** con el **MAYOR valor de z**, calculado por la **Suma Heurística Ponderada (Z)**, para maximizar la densidad de riesgo y anticipar el error más profundo del oponente.
3.  **Elección Aleatoria:** Si múltiples movimientos conducen a un resultado **T_z** idéntico y más alto, la elección del siguiente movimiento se realiza de forma **aleatoria**.

---

## 7. Caso de Estudio

El juego **Cruzados** (2x2, movimiento H/V/D, KO diagonal) es un ejemplo perfecto para demostrar la construcción completa del grafo de estados G, P, T y la propagación de la solución del DST.

---

## Autores

* Alex
* Logos

---

## Licencia

Este proyecto está bajo la **Licencia MIT**. Puedes usar, copiar, modificar y distribuir el código siempre y cuando se incluya la nota de copyright original.
