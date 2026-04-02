# Laboratorio: Ensamblado y Ejecución Paso a Paso
**Unidad 3: Manejo del DEBUG - Post-Contenido 2**  
**Asignatura:** Arquitectura de Computadores  
**Institución:** Universidad Francisco de Paula Santander (UFPS)  
**Estudiante:** [Tu Apellido]

---

## 1. Descripción del Laboratorio
[cite_start]Este laboratorio se centra en el uso del comando **T (Trace)** para la ejecución controlada de programas, permitiendo monitorear el cambio en los registros y banderas (flags) en cada ciclo de instrucción. [cite_start]Se analizan dos estructuras fundamentales: la ejecución lineal de una suma aritmética y la ejecución iterativa mediante la instrucción **LOOP**.

---

## 2. Checkpoint 1: Traza del Programa de Suma
[cite_start]Se ensambló un programa para sumar los valores 0Ah, 05h y 03h, acumulando el resultado en el registro AX.

### Tabla de Traza
| Instrucción | AX | BX | CX | IP Sig. | Flags |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `MOV AX, 000A` | 000A | 0000 | 0000 | 0103 | NV UP EI PL NZ NA PO NC |
| `MOV BX, 0005` | 000A | 0005 | 0000 | 0106 | NV UP EI PL NZ NA PO NC |
| `MOV CX, 0003` | 000A | 0005 | 0003 | 0109 | NV UP EI PL NZ NA PO NC |
| `ADD AX, BX`   | 000F | 0005 | 0003 | 010B | NV UP EI PL NZ NA PE NC |
| `ADD AX, CX`   | **0012** | 0005 | 0003 | 010D | **NV UP EI PL NZ AC PE NC** |

[cite_start]**Observación técnica:** En la captura 4 se observa que, tras las sumas, AX contiene `0012h` (18 decimal). [cite_start]Se destaca la activación de la bandera de ajuste **AC** (Auxiliary Carry) en la última suma, indicando un acarreo en el bit 3.

---

## 3. Checkpoint 2: Traza del Programa con Bucle (LOOP)
[cite_start]Se implementó un bucle que suma el valor `0002` cuatro veces, utilizando **CX** como contador.

### Tabla de Traza de Iteraciones
| Iteración | Instrucción | AX después | CX después | IP sig. | ¿LOOP salta? |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | `ADD AX, 0002` | 0002 | 0004 | 0109 | - |
| **1** | `LOOP 0106`    | 0002 | 0003 | 0106 | **SÍ** |
| **2** | `ADD AX, 0002` | 0004 | 0003 | 0109 | - |
| **2** | `LOOP 0106`    | 0004 | 0002 | 0106 | **SÍ** |
| **3** | `ADD AX, 0002` | 0006 | 0002 | 0109 | - |
| **3** | `LOOP 0106`    | 0006 | 0001 | 0106 | **SÍ** |
| **4** | `ADD AX, 0002` | **0008** | 0001 | 0109 | - |
| **4** | `LOOP 0106`    | 0008 | **0000** | **010B** | **NO** |

[cite_start]**Observación técnica:** Las capturas 1 y 2 muestran el decremento constante de **CX**. [cite_start]Cuando `CX` llega a `0000`, la instrucción `LOOP` ya no realiza el salto al offset `0106`, permitiendo que el programa avance a la instrucción `INT 20` para terminar.

---

## 4. Checkpoint 3: Análisis de Código Máquina (Paso 8)
[cite_start]Mediante el comando **D CS:100 LDC**, se analizó la codificación en bytes del programa de bucle.

| Instrucción | Bytes (Hex) | Longitud | Análisis |
| :--- | :--- | :--- | :--- |
| `MOV AX, 0000` | `B8 00 00` | 3 bytes | [cite_start]Opcode B8 + inmediato 0000. |
| `MOV CX, 0004` | `B9 04 00` | 3 bytes | [cite_start]Opcode B9 + inmediato 0004. |
| `ADD AX, 0002` | `05 02 00` | 3 bytes | [cite_start]Opcode 05 + inmediato 0002. |
| `LOOP 0106`    | `E2 FB`    | 2 bytes | [cite_start]E2 (LOOP) + salto relativo de -5 bytes (FB). |
| `INT 20`       | `CD 20`    | 2 bytes | [cite_start]Instrucción de interrupción de fin de programa. |

[cite_start]**Total:** El programa completo ocupa exactamente **13 bytes**.

---

## 5. Conclusiones
[cite_start]La práctica demuestra que la instrucción **LOOP** es una herramienta de control de flujo eficiente que depende estrictamente del registro **CX**. [cite_start]A través de la traza paso a paso, es posible visualizar cómo el procesador gestiona el desplazamiento relativo (`FB`) para volver a una dirección de memoria anterior mientras el contador sea distinto de cero.
