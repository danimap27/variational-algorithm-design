# Ansatze y Formas Variacionales en Algoritmos Cuánticos

## Introducción

En el núcleo de los algoritmos variacionales se encuentra la idea de analizar las diferencias entre estados, que están relacionadas a través de una mapeo parametrizable y bien definido (por ejemplo, continuo, diferenciable) desde un conjunto de parámetros o variables. Este enfoque permite explorar una colección de estados parametrizados en un algoritmo variacional.

Primero, exploraremos cómo construir circuitos parametrizados y usarlos para definir una forma variacional que representa una colección de estados parametrizados. Esta forma variacional se aplica al estado de referencia para crear el ansatz de nuestro algoritmo. Luego, analizaremos cómo equilibrar la velocidad y precisión en este espacio de búsqueda.

## Flujo de Trabajo del Ansatz

### Circuitos Cuánticos Parametrizados

Los algoritmos variacionales operan explorando y comparando un rango de estados cuánticos, que dependen de un conjunto finito de parámetros $\theta$. Estos estados pueden prepararse mediante un circuito cuántico parametrizado, en el que las puertas son definidas con parámetros ajustables.

Un circuito parametrizado permite realizar ajustes sin necesidad de especificar ángulos concretos desde el principio, proporcionando flexibilidad en la construcción de estados cuánticos durante la optimización del algoritmo.
```python
from qiskit.circuit import QuantumCircuit, Parameter

theta = Parameter("θ")

qc = QuantumCircuit(3)
qc.rx(theta, 0)
qc.cx(0, 1)
qc.x(2)

qc.draw("mpl")
```
```python
from math import pi

angle_list = [pi / 3, pi / 2]
circuits = [qc.assign_parameters({theta: angle}) for angle in angle_list]

for circuit in circuits:
    display(circuit.draw("mpl"))
```

### Forma Variacional y Ansatz

Para optimizar de un estado de referencia $| \rho \rangle$ a un estado objetivo $| \psi(\theta) \rangle$, se necesita definir una forma variacional $U_V(\theta)$, que representa una colección de estados parametrizados que explora nuestro algoritmo variacional. 

El estado parametrizado depende tanto del estado de referencia $| \rho \rangle$, que no depende de ningún parámetro, como de la forma variacional $U_V(\theta)$, que sí depende de parámetros. La combinación de ambos se denomina ansatz: $U_A(\theta) := U_V(\theta) U_R$.

## Limitaciones y Dimensionalidad

La construcción de un ansatz efectivo enfrenta un desafío importante: la dimensionalidad. Un sistema de $n$ -qubits tiene un gran número de estados distintos en su espacio de configuración, con una dimensionalidad de $D = 2^{2n}$. La complejidad de los algoritmos de búsqueda aumenta exponencialmente con esta dimensionalidad, conocida como la "maldición de la dimensionalidad".

Para contrarrestar esta limitación, es común imponer restricciones en la forma variacional para que solo los estados más relevantes sean explorados. El desarrollo de ansatzes truncados y eficientes es un área activa de investigación.

## Ansatze Heurísticos y Compromisos

En ausencia de información específica del problema que permita restringir la dimensionalidad, se puede intentar con una familia arbitraria de circuitos parametrizados que tengan menos de $2^{2n}$ parámetros. No obstante, esto implica algunos compromisos:

1. **Velocidad**: Al reducir el espacio de búsqueda, el algoritmo puede ejecutarse más rápidamente.
2. **Precisión**: Al reducir el espacio, se corre el riesgo de excluir la solución real del problema, lo que puede conducir a soluciones subóptimas.
3. **Ruido**: Circuitos más profundos son afectados por ruido, por lo que se requiere experimentar con la conectividad, las puertas y la fidelidad de las puertas del ansatz.

Existe un compromiso fundamental entre calidad y velocidad: cuanto más parámetros se utilicen, mayor es la probabilidad de encontrar un resultado preciso, pero mayor será el tiempo de ejecución del algoritmo.

## Circuitos N-local

Uno de los ejemplos más utilizados de ansatzes heurísticos son los circuitos N-local. Algunas de sus características son:

- **Implementación Eficiente**: Los ansatzes N-local suelen componerse de puertas locales simples, lo que facilita su implementación eficiente en un ordenador cuántico.
- **Correlación entre Qubits**: Los ansatzes N-local pueden capturar correlaciones importantes entre los qubits de un sistema cuántico, incluso con un número reducido de puertas.

Estos circuitos constan de capas de rotación y entrelazamiento que se alternan repetidamente, y se diseñan de la siguiente manera:

1. Cada capa tiene puertas de tamaño como máximo $N$, donde $N$ es menor que el número de qubits.
2. Las capas de rotación utilizan operaciones de rotación estándar, como $RX$ o $CRZ$.
3. Las capas de entrelazamiento pueden incluir puertas Toffoli o $CX$ con una estrategia de entrelazamiento.
4. Opcionalmente, se agrega una capa extra de rotación al final del circuito.
   
```python
from qiskit.circuit.library import NLocal, CCXGate, CRZGate, RXGate
from qiskit.circuit import Parameter

theta = Parameter("θ")
ansatz = NLocal(
    num_qubits=5,
    rotation_blocks=[RXGate(theta), CRZGate(theta)],
    entanglement_blocks=CCXGate(),
    entanglement=[[0, 1, 2], [0, 2, 3], [4, 2, 1], [3, 1, 0]],
    reps=2,
    insert_barriers=True,
)
ansatz.decompose().draw("mpl")
```

## TwoLocal

Los circuitos 2-local, o "TwoLocal", son un tipo común de circuito N-local, que suelen utilizar puertas de rotación de un solo qubit y puertas de entrelazamiento de 2-qubits, como las puertas $CNOT$. Estos circuitos se construyen con una distribución de entrelazamiento, donde cada qubit está entrelazado con el siguiente en una configuración lineal.

```python
from qiskit.circuit.library import TwoLocal

ansatz = TwoLocal(
    num_qubits=5,
    rotation_blocks=["rx", "rz"],
    entanglement_blocks="cx",
    entanglement="linear",
    reps=2,
    insert_barriers=True,
)
ansatz.decompose().draw("mpl")
```
## EfficientSU2

El circuito **EfficientSU2** es un circuito eficiente en hardware que consiste en capas de operaciones de un solo qubit que abarcan SU(2) y entrelazamientos $CX$. Este patrón heurístico puede utilizarse para preparar funciones de onda de prueba en algoritmos cuánticos variacionales o como un circuito de clasificación en aprendizaje automático cuántico.

```python
from qiskit.circuit.library import EfficientSU2

ansatz = EfficientSU2(4, su2_gates=["rx", "y"], entanglement="linear", reps=1)
ansatz.decompose().draw("mpl")
```

## Ansatze Específicos de Problemas

Los ansatzes heurísticos y eficientes en hardware permiten resolver un problema de manera general, pero al emplear conocimiento específico del problema, se puede restringir el espacio de búsqueda del circuito a un tipo específico de ansatz, acelerando el proceso de búsqueda sin perder precisión.

### Ejemplo de Optimización: Max-Cut

En un problema de max-cut, se quiere particionar los nodos de un grafo de manera que maximice el número de aristas entre nodos en diferentes grupos. Para resolver este problema con el algoritmo QAOA, se requiere un hamiltoniano de Pauli que codifique el costo, de manera que el valor mínimo de expectativa del operador corresponda al máximo número de aristas entre nodos de grupos diferentes.

```python
import rustworkx as rx
from rustworkx.visualization import mpl_draw

n = 4
G = rx.PyGraph()
G.add_nodes_from(range(n))
# The edge syntax is (start, end, weight)
edges = [(0, 1, 1.0), (0, 2, 1.0), (0, 3, 1.0), (1, 2, 1.0), (2, 3, 1.0)]
G.add_edges_from(edges)

mpl_draw(G, pos=rx.shell_layout(G), with_labels=True, edge_labels=str, node_color="#1192E8")
```

Por ejemplo, el operador para un grafo de 4 nodos podría ser una combinación lineal de términos con operadores Z en nodos conectados por una arista:

$ZZII + IZZI + ZIIZ + IZIZ + IIZZ$

Con este operador, el ansatz para el algoritmo QAOA puede construirse utilizando el circuito **QAOAAnsatz**.

```pyton
# Pre-defined ansatz circuit, operator class and visualization tools
from qiskit.circuit.library import QAOAAnsatz
from qiskit.quantum_info import SparsePauliOp

# Problem to Hamiltonian operator
hamiltonian = SparsePauliOp.from_list([("ZZII", 1), ("IZZI", 1), ("ZIIZ", 1), ("IZIZ", 1), ("IIZZ", 1)])
# QAOA ansatz circuit
ansatz = QAOAAnsatz(hamiltonian, reps=2)
# Draw
ansatz.decompose(reps=3).draw("mpl")
```

## Quantum Machine Learning

En machine learning, una aplicación común es la clasificación de datos en dos o más categorías. Esto implica codificar un punto de datos en un mapa de características que transforma vectores de características clásicas en el espacio de Hilbert cuántico. La construcción de mapas de características cuánticos basados en circuitos cuánticos parametrizados difíciles de simular clásicamente es un paso importante hacia una posible ventaja sobre los enfoques clásicos de machine learning, y constituye un área activa de investigación.

El **ZZFeatureMap** puede utilizarse para crear un circuito parametrizado. Podemos pasar los puntos de datos al mapa de características ($x$) y una forma variacional separada para introducir los pesos como parámetros ($θ$).

```python
from qiskit.circuit.library import ZZFeatureMap, TwoLocal

data = [0.1, 0.2]

zz_feature_map_reference = ZZFeatureMap(feature_dimension=2, reps=2)
zz_feature_map_reference = zz_feature_map_reference.assign_parameters(data)

variation_form = TwoLocal(2, ["ry", "rz"], "cz", reps=2)
vqc_ansatz = zz_feature_map_reference.compose(variation_form)
vqc_ansatz.decompose().draw("mpl")
```




