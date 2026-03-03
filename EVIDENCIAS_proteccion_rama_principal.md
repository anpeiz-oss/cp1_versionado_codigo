Con el objetivo de evitar los commits directos en la rama principal (master en el caso particular que nos ocupa) y que todos los cambios se tengan que introducir en base a Pull Requests, crearemos un nuevo RuleSet en el apartado Settings del repositorio de github, al que denominaremos “Proteger rama principal”. Dentro de dicho ruleset :
* crearemos un bypass list en la que incluiremos únicamente al rol Repository admin.
* Como Target branches indicaremos la rama master.
* Como Branch rules marcaremos las siguientes opciones:
** Restrict creations, Restrict updates y Restrict deletions
*** Con estas opciones activas, solo el rol/perfil Repository admin, indicado anteriormente podrá crear, actualizar y/o borrar la rama master. De esta forma nos aseguramos que la rama master no pueda ser sustituida por una nueva.
** Require signed commits
*** Esta opción no es realmente necesaria, pero aporta un nivel adicional de seguridad, que nunca está de más.
** Require a pull request before merge
*** Esta es la opción que tenemos que marcar si o si, para cumplir de forma mínima con el enunciado.
*** Required approvals: 1
**** Deberemos requerir que al menos exista una aprobación del pull request, de lo contrario, se haya aprobado o no dicha PR, se permitiría la incorporación de los cambios, y entiendo que eso no es lo que se desea cuando se introduce esta restricción.
** Block force pushes
*** Esta opción también debería estar activada siempre en cualquier repositorio, de lo contrario se podrían pisar commits y generarse situaciones no deseadas.

Al margen de las opciones indicadas que, bajo mi criterio muy enfocado en la integridad del código, con el principal objetivo de evitar que se introduzcan cambios no controlados en el código base del repositorio, sería conveniente introducir las opciones de escaneo de código en busca de potenciales problemas de seguridad en el mismo, así como evaluadores de la calidad del mismo y análisis de cobertura de tests.

* Justificación de bloqueo de la rama principal
Basándome en mis vivencias y preferencias, que he ido acumulando a lo largo de mi trayectoria profesional gestionando repositorios de código fuente, no solo veo recomendable, sino más bien necesario, evitar que se trabaje directamente sobre la rama principal (TBD) por los siguientes motivos:
* Fuerza a establecer puntos de control previos a la incorporación de nuevo código (sean correcciones o nuevas funcionalidades). Estos puntos de control suelen automatizarse total o parcialmente, pero en cualquier caso dejan rastro y levantan alertas para tomar medidas si algo no se ajusta a lo que se esperaba, actuando de forma proactiva en lugar de reactiva ante los errores.

* Cuando se supera el punto de control que permite la incorporación de nuevo código en la rama principal, es porque se han superado los controles definidos previamente y, por tanto, el código es elegible para ser incorporado, si bien esto no garantiza el 100% de seguridad (depende de lo acertados, o no, que hayan estado los criterios establecidos en el punto de control), sí que reduce enormemente la posibilidad de introducir problemas en el código, y el tiempo de respuesta y el coste de corregir los posibles errores/problemas se reduce drásticamente.

* Otro punto a favor de este enfoque, si bien esto ya entra más en mis preferencias personales y enfoque, es que de alguna forma te lleva a la necesidad de simplificar o serializar la inclusión de cambios en el repositorio, o incluso iría más allá, a mejorar el análisis (especialmente en el contexto de los cambios correctivos) de tareas a abordar sobre el código (evitaremos generar 2 tareas de corrección que afecten al mismo bloque de código o al menos no se las asignaremos a 2 programadores distintos para que las aborden de forma simultánea, pues van a provocar conflictos bien a nivel sintáctico o bien semántico, lo que es más complejo de gestionar). Es decir, el hecho de tener que evaluar código nuevo, fuerza a que se haya de tomar una versión estable como base de contraste, y esto hace que de alguna forma no sea buena idea abrir 2 procesos de validación concurrentes contra una misma versión. Pero esta es una segunda o tercera derivada de la que podríamos estar hablando largo y tendido.
