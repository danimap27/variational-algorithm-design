# Estados de Referencia en Algoritmos Variacionales

## Introducción
Los **estados de referencia** son configuraciones iniciales específicas en algoritmos variacionales cuánticos que ayudan a que la optimización converja más rápido. Estos estados se preparan aplicando operadores unitarios no parametrizados y permiten que el sistema comience en una configuración inicial más informativa, acercando el sistema a una solución óptima.

## 1. Estado de Referencia Predeterminado
El **estado predeterminado** es la configuración inicial más simple en un sistema cuántico, en la que todos los qubits están en el estado base $|0\rangle^{\otimes n}$, es decir, en estado $|0\rangle$.

- **Definición**: El estado se representa como $|\rho\rangle = U_R |0\rangle$, donde $U_R$ es el operador identidad.
- **Implementación**: No requiere compuertas adicionales; simplemente se utiliza el estado base del circuito cuántico.

Este tipo de estado es útil en situaciones donde no se tiene una idea clara del estado óptimo y permite que los algoritmos prueben configuraciones desde una base neutral.


## 2. Estado de Referencia Clásico
El **estado de referencia clásico** es un estado inicial en una configuración específica clásica, por ejemplo, $|001\rangle$. Este tipo de estado es útil cuando se conoce una configuración cercana a la solución y se desea comenzar en un estado que simplifique la optimización.
```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(3)
qc.x(0)

qc.draw("mpl")
```
### Ejemplo: Estado $|001\rangle$
Para un sistema de 3 qubits, el estado $|001\rangle$ se obtiene aplicando una compuerta que cambia el primer qubit a $|1\rangle$ mientras los otros dos permanecen en $|0\rangle$.

- **Expresión matemática**: Se representa como $|\rho\rangle = X_0 |000\rangle = |001\rangle$, donde $X_0$ es una operación que solo actúa en el primer qubit.

Este método es ventajoso cuando se tiene una configuración inicial que puede ayudar a simplificar el problema o que se aproxima al estado óptimo.

## 3. Estado de Referencia Cuántico
Un **estado de referencia cuántico** permite inicializar el sistema en un estado más complejo, introduciendo superposición y/o entrelazamiento. Estos estados permiten explorar configuraciones ricas en información desde el inicio, lo que puede facilitar la convergencia en menos iteraciones.

### Ejemplo: Estado $\frac{1}{\sqrt{2}}(|100\rangle + |111\rangle)$
Para crear un estado como $\frac{1}{\sqrt{2}}(|100\rangle + |111\rangle)$ en un sistema de 3 qubits, se utiliza una serie de operaciones cuánticas:

1. Se aplica una superposición en el primer qubit.
2. Luego, se introduce entrelazamiento entre el primer y segundo qubit.
3. Finalmente, se ajusta el tercer qubit para obtener la configuración deseada.

- **Expresión matemática**: Este estado se representa como $|\rho\rangle = X_2 \cdot \text{CNOT}_{01} \cdot H_0 |000\rangle = \frac{1}{\sqrt{2}}(|100\rangle + |111\rangle)$.

Este tipo de estado es útil en problemas que se benefician de configuraciones de probabilidad más complejas o de patrones específicos de superposición y entrelazamiento.
```python
qc = QuantumCircuit(3)
qc.h(0)
qc.cx(0, 1)
qc.x(2)

qc.draw("mpl")
```
## 4. Estado de Referencia Específico para Aplicaciones
Un estado de referencia específico para aplicaciones es un estado personalizado para un propósito particular, como en **aprendizaje automático cuántico** (Quantum Machine Learning, QML). En QML, un **mapa de características** (*feature map*) se utiliza para codificar los datos en un estado cuántico. Estos estados permiten que cada valor de un conjunto de datos esté representado en el sistema cuántico desde el inicio, optimizando la convergencia del algoritmo.

### Ejemplo: ZZFeatureMap
El ZZFeatureMap es un tipo de mapa de características que codifica la información de entrada en el estado cuántico mediante entrelazamiento y superposición, ideal para representar datos de entrenamiento. Supongamos que se tienen datos de entrada que representan características numéricas y que se quiere mapear en el estado cuántico mediante un estado de referencia ajustado.

Este enfoque permite incorporar los datos directamente en el circuito cuántico, lo cual es esencial en aplicaciones de **clasificación cuántica**, donde los datos son representados mediante un *feature map* que optimiza la precisión y convergencia del modelo.
```python
from qiskit.circuit.library import ZZFeatureMap

data = [0.1, 0.2]

zz_feature_map_reference = ZZFeatureMap(feature_dimension=2, reps=2)
zz_feature_map_reference = zz_feature_map_reference.assign_parameters(data)
zz_feature_map_reference.decompose().draw("mpl")
```
## Resumen
Elegir el estado de referencia adecuado es crucial en los algoritmos variacionales cuánticos, ya que puede reducir las iteraciones necesarias para encontrar la solución óptima, mejorando así la eficiencia. Los tipos de estados de referencia incluyen:

- **Estado predeterminado**: Comienza desde el estado base $|0\rangle^{\otimes n}$.
- **Estado clásico**: Utiliza configuraciones de estados base clásicos, como $|001\rangle$.
- **Estado cuántico**: Emplea configuraciones complejas que incluyen superposición y entrelazamiento.
- **Estado específico para aplicaciones**: Estado adaptado para una aplicación particular, como los mapas de características en Quantum Machine Learning.

Elegir el estado de referencia adecuado ofrece una ventaja significativa en términos de rendimiento y precisión del algoritmo, y resulta fundamental para optimizar los recursos cuánticos.
