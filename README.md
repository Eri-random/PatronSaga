# Patrón Saga

### Transacciones

La función de ejecutar varias instrucciones como un todo se conoce como transacción de base de datos. Una transacción de base de datos garantiza que todas las operaciones dentro del alcance de esa transacción tengan éxito o fracasen.

Las transacciones tienen cuatro propiedades: atomicidad, consistencia, aislamiento y persistencia. Estas cuatro propiedades se conocen comúnmente como características ACID.

* Atomicidad: todas las operaciones de una transacción se completan o no, pero nunca terminan en algún punto intermedio.

* Coherencia: la integridad de la base de datos no se ve afectada antes de que comience la transacción ni después de que esta finalice.

* Aislamiento: La base de datos permite que varias transacciones simultáneas lean, escriban y modifiquen sus datos al mismo tiempo. El aislamiento evita inconsistencias en los datos debido a la ejecución cruzada cuando se ejecutan varias transacciones simultáneamente.

* Persistencia: Una vez finalizada la transacción, la modificación de los datos es permanente y no se perderán aunque el sistema falle.

### Transacciones distribuidas

Una transacción distribuida significa que el iniciador de la transacción, el administrador de recursos  y el coordinador de la transacción se encuentran en diferentes nodos del sistema distribuido. 

#### Confirmación de dos fases/XA

XA es una especificación para transacciones distribuidas propuesta por la organización X/Open. La especificación XA define principalmente la interfaz entre un administrador de transacciones (TM) (global) y un administrador de recursos (RM) (local).

XA se divide en dos fases.

* Fase 1 (preparación): todos los RM participantes se preparan para ejecutar sus transacciones y bloquear los recursos necesarios. Cuando los participantes están listos, informan a TM que están listos.
      
* Fase 2 (confirmación/reversión): cuando el administrador de transacciones (TM) confirma que todos los participantes (RM) están listos, envía un comando de confirmación a todos los participantes.
  
Una transacción XA consta de uno o más administradores de recursos (RM), un administrador de transacciones (TM) y un programa de aplicación (AP).
Si falla una operación de la fase de preparación de cualquier participante, DTM llamará a xa rollback de cada transacción secundaria para revertirla, y la transacción se revierte exitosamente al final.

#### SAGA

La idea clave es escribir una transacción de larga duración como múltiples transacciones locales cortas, denominadas colectivamente SAGA y coordinadas por el coordinador de transacciones SAGA. Si todas las subtransacciones de una SAGA se completan, la SAGA se completa con éxito. Si una subtransacción falla, las transacciones compensatorias se invocarán una a la vez en el orden inverso.

Las características de las transacciones SAGA son:
* Alta concurrencia, sin bloqueo de recursos a largo plazo como las transacciones XA
* Es necesario definir el funcionamiento normal y el funcionamiento compensatorio, por lo que la demanda de desarrollo es mayor que XA.
* Consistencia débil. Si tomamos como ejemplo la transferencia de dinero, el estado intermedio en el que el dinero se ha deducido de la cuenta del usuario A pero la transferencia finalmente falla puede verse

#### ¿Qué es el patrón de arquitectura Saga?

El patrón de arquitectura Saga  proporciona gestión de transacciones utilizando una secuencia de transacciones locales.
Una transacción local es la unidad de trabajo realizada por un participante de Saga. Cada operación que forma parte de Saga puede revertirse mediante una transacción compensatoria. Además, el patrón Saga garantiza que todas las operaciones se completen correctamente o que se ejecuten las transacciones compensatorias correspondientes para deshacer el trabajo realizado anteriormente.
En el patrón Saga,  una transacción compensatoria debe ser  idempotente  y  reintentable . Estos dos principios garantizan que podamos gestionar transacciones sin ninguna intervención manual.

![image](https://github.com/user-attachments/assets/c2d1ef83-18e4-40fd-a9ab-903f186e48eb)

#### El Coordinador de Ejecución de la Saga

El  coordinador de ejecución de Saga es el componente central para implementar un flujo de Saga. Contiene un registro de Saga que captura la secuencia de eventos de una transacción distribuida.
En caso de falla, el componente SEC inspecciona el registro de Saga para identificar los componentes afectados y la secuencia en que deben ejecutarse las transacciones compensatorias.
En caso de que se produzca un fallo en el componente SEC, podrá leer el registro de Saga una vez que vuelva a estar en funcionamiento.
Luego puede identificar las transacciones revertidas con éxito, cuáles están pendientes y puede tomar las medidas apropiadas:
Existen dos enfoques para implementar el patrón Saga: la coreografía y la orquestación. 

#### Implementación del patrón de orquestación de Saga

En el patrón de orquestación,  un único orquestador es responsable de gestionar el estado general de la transacción.
Si alguno de los microservicios encuentra una falla, el orquestador es responsable de invocar las transacciones de compensación necesarias:

![image](https://github.com/user-attachments/assets/5a92ef05-39a1-48da-8418-dec54ecd109a)

El patrón de orquestación Saga es  útil para la arquitectura de desarrollo de aplicaciones de microservicios brownfield. En otras palabras, este patrón funciona cuando ya tenemos un conjunto de microservicios y nos gustaría implementar el patrón Saga en la aplicación. Necesitamos definir las transacciones de compensación adecuadas para continuar con este patrón.

https://www.baeldung.com/cs/saga-pattern-microservices

https://medium.com/@dongfuye/the-seven-most-classic-solutions-for-distributed-transaction-management-3f915f331e15

#### Apache Camel

https://camel.apache.org/components/4.8.x/eips/saga-eip.html

