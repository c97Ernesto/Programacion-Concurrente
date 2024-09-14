


## Práctica 2 - Semáforos
### Sintaxis
**Declaraciones de semáforos**

- `sem s; ` --> No se puede. Si o si se deben inicializar en la declaración.
- `sem mutex = 1;`
- `sem espera[5] = ([5] 1);`

**Operaciones de los Semáforos**
- **`P(s)`** -> `< await (s > 0) s = s-1; >`
- **`V(s)`** -> `< s = s + 1; >`

### Ejercicios

1. Existen N personas que deben ser chequeadas por un detector de metales antes de poder ingresar al avión.

    1. Analice el problema y defina qué procesos, recursos y semáforos/sincronizaciones serán necesarios/convenientes para resolverlo.

    2. Implemente una solución que modele el acceso de las personas a un detector (es decir, si el detector está libre la persona lo puede utilizar; en caso contrario, debe esperar).
        ```java
        sem mutex = 1;

        Process Persona[id: 0..N-1]{
            P(mutex)        /*esperar hasta que el detector esté disponible*/
            //usar detector
            V(mutex)    /*habilitar detector*/
        }
        ```

    3. Modifique su solución para el caso que haya tres detectores.
        ```java
        sem mutex = 3;

        Process Persona[id: 0..N-1]{
            P(mutex)        /*esperar hasta que el detector esté disponible*/
            //usar detector
            V(mutex)    /*habilitar detector*/
        }
        ```

    4. Modifique la solución anterior para el caso en que cada persona pueda pasar más de una vez, siendo aleatoria esa cantidad de veces.
        ```java
        sem mutex = 3;

        Process Persona[id: 0..N-1]{
            
        }
        ```


   
2. Un sistema de control cuenta con 4 procesos que realizan chequeos en forma colaborativa. Para ello, reciben el historial de fallos del día anterior (por simplicidad, de tamaño N). De cada fallo, se conoce su número de identificación (ID) y su nivel de gravedad (0=bajo, 1=intermedio, 2=alto, 3=crítico). Resuelva considerando las siguientes situaciones:
   
   1. Se debe imprimir en pantalla los ID de todos los errores críticos (no importa elorden).

        ```java
        Sem mutex = 4; int i = 0
        int fallos[N]; 

        Process Control[c: 0..3] {
            Fallo fallo;
            P(mutex)    /*bloqueo para no acceder a una pos que no existe*/
            while (i < N ) {
                fallo = fallos[i];
                i++
                V(mutex) /*libero para que otros puedan entrar a verificar los fallos restantes*/
                if (fallo.nivelGravedad == 3) {
                    Print(fallo.id);
                }
                P(mutex) /*vuelvo a bloquear para poder hacer la verificación del while*/
            }
            V(mutex)
        }
        ```

   2. Se debe calcular la cantidad de fallos por nivel de gravedad, debiendo quedar los resultados en un vector global.
        ```java
        Sem mutex = 4; int i = 0;
        int fallos[N]; 
        int nivelGravedad[n] = ([n], 0);

        Process Control[c: 0..3] {
            Fallo fallo;
            P(mutex)    /*bloqueo para no acceder a una pos que no existe*/
            while (i < N ) {
                fallo = fallos[i];
                i++;
                V(mutex) /*libero para que otros puedan entrar a verificar los fallos restantes*/
                
                /*incrementar array global nivelGravedad*/
                 
                P(mutex) /*vuelvo a bloquear para poder hacer la verificación del while*/
            }
            V(mutex)
        }
        ```

   3. Ídem b) pero cada proceso debe ocuparse de contar los fallos de un nivel de gravedad determinado.

        ```
        ```

3. Un sistema operativo mantiene 5 instancias de un recurso almacenadas en una cola. Además, existen P procesos que necesitan usar una instancia del recurso. Para eso, deben sacar la instancia de la cola antes de usarla. Una vez usada, la instancia debe ser encolada nuevamente para su reúso.

    ```

    ```

4. Suponga que existe una BD que puede ser accedida por 6 usuarios como máximo al mismo tiempo. Además, los usuarios se clasifican como usuarios de prioridad alta y usuarios de prioridad baja. Por último, la BD tiene la siguiente restricción:
    - no puede haber más de 4 usuarios con prioridad alta al mismo tiempo usando la BD.
    - no puede haber más de 5 usuarios con prioridad baja al mismo tiempo usando la BD.

    Indique si la solución presentada es la más adecuada. Justifique la respuesta.

    ```java
    Var
        total: sem := 6;
        alta: sem := 4;
        baja: sem := 5;

    Process Usuario-Alta [I:1..L]::
    {   P (total);
        P (alta);
        //usa la BD
        V(total);
        V(alta);
    }

    Process Usuario-Baja [I:1..K]::
    {   P (total);
        P (baja);
        //usa la BD
        V(total);
        V(baja);
    }
    ```

5. En una empresa de logística de paquetes existe una sala de contenedores donde se preparan las entregas. Cada contenedor puede almacenar un paquete y la sala cuenta con capacidad para N contenedores. Resuelva considerando las siguientes situaciones:

    1. La empresa cuenta con 2 empleados: un empleado Preparador que se ocupa de preparar los paquetes y dejarlos en los contenedores; un empelado Entregador que se ocupa de tomar los paquetes de los contenedores y realizar la entregas.
    Tanto el Preparador como el Entregador trabajan de a un paquete por vez.
        ```java
        sem paquete = 0;
        Buffer<Paquete> [N] contenedores;

        Process Preparador{
            Paquete paquete;
            while (true){
                GenerarPaquete(paquete);
                contenedores.push(paquete);
                V(paquete)  /*habilito un paquete (paquete++)*/
            }
        }

        Process Entregador{
            Paquete paquete;
            while (true) {
                P(paquete);  /*verifico si hay un paquete disponible (<await (paquete > 0); paquete--;>)*/
                conenedores.pop(paquete);
            }
        }
        ```    

    2. Modifique la solución a) para el caso en que haya P empleados Preparadores.
        ```java
        sem mutex = 1; sem paquete = 0;
        Buffer<Paquete> [N] contenedores;

        Process Preparador[idP: 1..P]{
            Paquete paquete;
            while (true){
                GenerarPaquete(paquete);
                P(mutex)    /*verifico si la SC está habilitada*/
                contenedores.push(paquete);
                V(mutex)    /*habilito SC*/
                V(paquete)  /*habilito un paquete (paquete++)*/
            }
        }

        Process Entregador{
            Paquete paquete;
            while (true) {
                P(paquete);  /*verifico si hay un paquete disponible (<await (paquete > 0); paquete--;>)*/
                P(mutex);    /*verifico si la SC se encuentra habilitada*/
                conenedores.pop(paquete);
                V(mutex);
            }
        }
        ```    

    3. Modifique la solución a) para el caso en que haya E empleados Entregadores.
        ```java
        sem mutex = 1; sem paquete = 0;
        Buffer<Paquete> [N] contenedores;

        Process Preparador{
            Paquete paquete;
            while (true){
                GenerarPaquete(paquete);
                P(mutex)    /*verifico si la SC está habilitada*/
                contenedores.push(paquete);
                V(mutex)    /*habilito SC*/
                V(paquete)  /*habilito un paquete (paquete++)*/
            }
        }

        Process Entregador[idE: 1..E]{
            Paquete paquete;
            while (true) {
                P(paquete);  /*verifico si hay un paquete disponible (<await (paquete > 0); paquete--;>)*/
                P(mutex);    /*verifico si la SC se encuentra habilitada*/
                conenedores.pop(paquete);
                V(mutex);
            }
        }
        ```    

    4. Modifique la solución a) para el caso en que haya P empleados Preparadores y E empleadores Entregadores.
        ```java
        ```

6. Existen N personas que deben imprimir un trabajo cada una. Resolver cada ítem usando semáforos:

    1. Implemente una solución suponiendo que existe una única impresora compartida por todas las personas, y las mismas la deben usar de a una persona a la vez, sin importar el orden. Existe una función Imprimir(documento) llamada por la persona que simula el uso de la impresora. Sólo se deben usar los procesos que representan a las Personas.

    2. Modifique la solución de (a) para el caso en que se deba respetar el orden de llegada.

    3. Modifique la solución de (a) para el caso en que se deba respetar estrictamente el orden dado por el identificador del proceso (la persona X no puede usar la impresora hasta que no haya terminado de usarla la persona X-1)

    4. Modifique la solución de (b) para el caso en que además hay un proceso Coordinador que le indica a cada persona que es su turno de usar la impresora.

    5. Modificar la solución (d) para el caso en que sean 5 impresoras. El coordinador le indica a la persona cuando puede usar una impresora, y cual debe usar.

7. Suponga que se tiene un curso con 50 alumnos. Cada alumno debe realizar una tarea y existen 10 enunciados posibles. Una vez que todos los alumnos eligieron su tarea, comienzan a realizarla. Cada vez que un alumno termina su tarea, le avisa al profesor y se queda esperando el puntaje del grupo (depende de todos aquellos que comparten el mismo enunciado). Cuando un grupo terminó, el profesor les otorga un puntaje que representa el orden en que se terminó esa tarea de las 10 posibles.

    **Nota:** Para elegir la tarea suponga que existe una función elegir que le asigna una tarea a un alumno (esta función asignará 10 tareas diferentes entre 50 alumnos, es decir, que 5 alumnos tendrán la tarea 1, otros 5 la tarea 2 y así sucesivamente para las 10 tareas).

8. Una fábrica de piezas metálicas debe producir T piezas por día. Para eso, cuenta con E empleados que se ocupan de producir las piezas de a una por vez. La fábrica empieza a producir una vez que todos los empleados llegaron. Mientras haya piezas por fabricar, los empleados tomarán una y la realizarán. Cada empleado puede tardar distinto tiempo en fabricar una pieza. Al finalizar el día, se debe conocer cual es el empleado que más piezas fabricó.

    1. Implemente una solución asumiendo que T > E.
    2. Implemente una solución que contemple cualquier valor de T y E.