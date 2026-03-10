# Versionado de código y Colaboración

Enlace repositorio en github: <https://github.com/anpeiz-oss/cp1_versionado_codigo>

## Protección de la rama principal

Con el objetivo de evitar los commits directos en la rama principal (master en el caso particular que nos ocupa) y que todos los cambios se tengan que introducir en base a Pull Requests, crearemos un nuevo RuleSet en el apartado Settings del repositorio de github, al que denominaremos “Proteger rama principal”. Dentro de dicho ruleset :

* crearemos un bypass list en la que incluiremos únicamente al rol Repository admin.
* Como Target branches indicaremos la rama master.
* Como Branch rules marcaremos las siguientes opciones:
  * Restrict creations, Restrict updates y Restrict deletions
        * Con estas opciones activas, solo el rol/perfil Repository admin, indicado anteriormente podrá crear, actualizar y/o borrar la rama master. De esta forma nos aseguramos que la rama master no pueda ser sustituida por una nueva.
  * Require signed commits
    * Esta opción no es realmente necesaria, pero aporta un nivel adicional de seguridad, que nunca está de más.
  * Require a pull request before merge
    * Esta es la opción que tenemos que marcar si o si, para cumplir de forma mínima con el enunciado.
    * Required approvals: 1
      * Deberemos requerir que al menos exista una aprobación del pull request, de lo contrario, se haya aprobado o no dicha PR, se permitiría la incorporación de los cambios, y entiendo que eso no es lo que se desea cuando se introduce esta restricción.
  * Block force pushes
    * Esta opción también debería estar activada siempre en cualquier repositorio, de lo contrario se podrían pisar commits y generarse situaciones no deseadas.

Al margen de las opciones indicadas que, bajo mi criterio muy enfocado en la integridad del código, con el principal objetivo de evitar que se introduzcan cambios no controlados en el código base del repositorio, sería conveniente introducir las opciones de escaneo de código en busca de potenciales problemas de seguridad en el mismo, así como evaluadores de la calidad del mismo y análisis de cobertura de tests.

### Justificación de bloqueo de la rama principal

Basándome en mis vivencias y preferencias, que he ido acumulando a lo largo de mi trayectoria profesional gestionando repositorios de código fuente, no solo veo recomendable, sino más bien necesario, evitar que se trabaje directamente sobre la rama principal (TBD) por los siguientes motivos:

* Fuerza a establecer puntos de control previos a la incorporación de nuevo código (sean correcciones o nuevas funcionalidades). Estos puntos de control suelen automatizarse total o parcialmente, pero en cualquier caso dejan rastro y levantan alertas para tomar medidas si algo no se ajusta a lo que se esperaba, actuando de forma proactiva en lugar de reactiva ante los errores.

* Cuando se supera el punto de control que permite la incorporación de nuevo código en la rama principal, es porque se han superado los controles definidos previamente y, por tanto, el código es elegible para ser incorporado, si bien esto no garantiza el 100% de seguridad (depende de lo acertados, o no, que hayan estado los criterios establecidos en el punto de control), sí que reduce enormemente la posibilidad de introducir problemas en el código, y el tiempo de respuesta y el coste de corregir los posibles errores/problemas se reduce drásticamente.

* Otro punto a favor de este enfoque, si bien esto ya entra más en mis preferencias personales y enfoque, es que de alguna forma te lleva a la necesidad de simplificar o serializar la inclusión de cambios en el repositorio, o incluso iría más allá, a mejorar el análisis (especialmente en el contexto de los cambios correctivos) de tareas a abordar sobre el código (evitaremos generar 2 tareas de corrección que afecten al mismo bloque de código o al menos no se las asignaremos a 2 programadores distintos para que las aborden de forma simultánea, pues van a provocar conflictos bien a nivel sintáctico o bien semántico, lo que es más complejo de gestionar). Es decir, el hecho de tener que evaluar código nuevo, fuerza a que se haya de tomar una versión estable como base de contraste, y esto hace que de alguna forma no sea buena idea abrir 2 procesos de validación concurrentes contra una misma versión. Pero esta es una segunda o tercera derivada de la que podríamos estar hablando largo y tendido.

## Gestión de ficheros a ignorar

Se han añadido al fichero .gitignore las siguientes entradas:

* *.log -> con esta opción se ignoran todos aquellos ficheros cuya extensión sea .log y están ubicados en cualquier ubicación del repositorio.

* __pycache__/ -> con esta opción se ignora el directorio __pycache__ en cualquier ubicación dentro del repositorio.

## Conflictos: descripción de aparición y resolución del mismo

* Tras haber aceptado la PR de la feature suma, nos encontramos que ahora la feature de resta, presenta un conflicto a la hora de compararla con la rama master, pues se ha actualizado el mismo fichero (operations.py) en master y este mismo fichero también ha sido modificado en la rama feature/resta, pero estos cambios realizados en master, no existen, ni han sido trasladados la rama feature/resta. Es más, en su lugar existen otros cambios que no han sido trasladados a master todavía, esta situación es la que se identifica como un conflicto, cambios realizados en un mismo fichero que no son consecuentes en las 2 ramas en comparación.

* Para resolver la situación de conflicto, lo que haremos es traernos todos los cambios aceptados en la rama master de github a nuestro repositorio local. Una vez tengamos nuestra rama master local actualizada, mergearemos dicha rama en la feature/resta con el objetivo de que esta contenga las novedades introducidas en la feature/suma que no poseía hasta el momento y que eran la fuente del conflicto. En este proceso tendremos que conseguir juntar los cambios de master con las novedades introducidas en el desarrollo de la feature resta y que todo siga siendo válido a nivel sintáctico y funcional. Una vez alcanzado este punto, ya estaremos en condiciones de mergear la rama feature/resta en master, pues el conflicto ha sido resuelto trayendonos estos cambios faltantes desde master y añadiendo las nuevas lineas de códgo correspondientes al desarrollo de la feature.

## Workflow en Github Actions

Para incorporar el un nuevo workflow que sea interpretable por github deberemos crear un fichero .yml bajo la carpeta .github/workflows/ en el caso que nos ocupa, crearemos el fichero demo.yml en el que definiremos el flujo siguiente:

name: Manual workflow

Esta definición controla cuando la acción se ejecutará. Este workflow se ejecutará manualmente desde la Interfaz de Usuario o bien mediante una llamada remota directa contra la API.

* on:
  * workflow_dispatch:
    * // Entradas que aceptará cada una de las ejecuciones del workflow.
    * inputs:
      * nombre:
        * // Descripción a mostrar en el momento de introducir la entrada por la Interfaz de Usuario en lugar de simplemente 'nombre'.
        * description: 'Nombre de la persona cuyo nombre se desea mostrar por pantalla.'
        * // Valor por defecto de la entrada si no se proporcina uno explicitamente.
        * default: 'Angel'
        * // Indica si el valor de este campo debe ser proporcionado para que el workflow se ejecute o no.
        * required: true
        * // El tipo de datos del campo de entrada.
        * type: string
      * apellidos:
        * // Descripción a mostrar en el momento de introducir la entrada por la Interfaz de Usuario en lugar de simplemente 'apellidos'.
        * description: 'Apellidos de la persona cuyos apellidos se desea mostrar por pantalla.'
        * // Valor por defecto de la entrada si no se proporcina uno explicitamente.
        * default: 'Pérez Izquierdo'
        * // Indica si el valor de este campo debe ser proporcionado para que el workflow se ejecute o no.
        * required: true
        * // El tipo de datos del campo de entrada.
        * type: string

* // Una ejecución de workflow se compone de uno o más trabajos que pueden ejecutarse secuencialmente o en paralelo

* jobs:
  * // Este workflow está compuesta por una única tarea simple denominada "saludo"
  * saludo:
    * // Indica el tipo de ejecutar que va a ejecutar la secuencia de tareas/trabajos definidos a continuación.
    * runs-on: ubuntu-latest

  * // Steps representa una secuencia de tareas o pasos a ejecutar dentro de un trabajo.
    * steps:
    * // Ejecuta un comando simple ejecutando el ejecutor del shell por defectodel sistema operativo ubuntu.
      * -name: Enviar saludo
      * run: echo "Hola ${{ inputs.nombre }} ${{ inputs.apellidos }}! este es un saludo lanzado de forma manual desde un github action, especialmente para ti."
