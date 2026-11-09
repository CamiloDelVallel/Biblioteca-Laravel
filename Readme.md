# Sistema de gestión de una biblioteca con Laravel

## 1. Problema

La biblioteca **Entre Páginas** administra sus libros, lectores y préstamos en hojas de cálculo. Esto provoca registros duplicados, dificultades para encontrar libros y desconocimiento de los ejemplares disponibles. Además, no cuenta con un catálogo que permita consultar las portadas y la información de sus libros.

Tu equipo deberá analizar el problema, diseñar la base de datos y desarrollar una aplicación web con Laravel que permita administrar la biblioteca y consultar su catálogo.

## 2. Objetivo

Construir una aplicación funcional que integre operaciones CRUD sobre **seis entidades de negocio**, relaciones entre tablas, filtros, carga de imágenes, validaciones y autenticación. El diseño y la implementación deben coincidir.

## 3. Alcance

La aplicación tendrá dos espacios:

- **Catálogo público:** cualquier visitante podrá consultar libros, ver sus detalles y aplicar filtros sin iniciar sesión.
- **Panel administrativo:** un administrador autenticado podrá gestionar las seis entidades y registrar préstamos y devoluciones.

Los lectores se registran como información de la biblioteca: no tendrán cuenta ni iniciarán sesión. Los préstamos se registran desde el panel administrativo.

No se requieren pagos, multas, reservas, notificaciones ni integración con servicios externos. Cada préstamo corresponderá a un libro y una unidad de ese libro; no se manejarán ejemplares individuales con códigos propios.

## 4. Seis entidades con CRUD obligatorio

Cada entidad debe permitir **crear, listar, consultar el detalle, editar y eliminar**, respetando las restricciones de las reglas de negocio.

| Entidad | Datos mínimos | Qué se debe poder gestionar |
| --- | --- | --- |
| Categoría | Nombre único y descripción | Categorías como literatura, tecnología e historia. |
| Autor | Nombre, nacionalidad, biografía y fotografía opcional | Autores que se asociarán a los libros. |
| Editorial | Nombre único, correo opcional, teléfono opcional y ciudad | Editoriales responsables de las publicaciones. |
| Libro | Título, ISBN único, descripción, año de publicación, cantidad total, portada, categoría, autor y editorial | Libros del catálogo y sus existencias. |
| Lector | Nombre, documento único, correo y teléfono | Personas que reciben libros prestados. |
| Préstamo | Lector, libro, fecha de préstamo, fecha límite, fecha real de devolución opcional y observaciones opcionales | Préstamos y registro de sus devoluciones. |

Todas deben incluir identificador y marcas de tiempo. La cuenta de administrador y las tablas técnicas de Laravel son adicionales: **no cuentan dentro de las seis entidades exigidas**. No se exige CRUD de administradores.

### Relaciones del modelo

- Una categoría puede tener muchos libros; cada libro pertenece a una categoría.
- Un autor puede tener muchos libros; para esta actividad, cada libro tiene un único autor.
- Una editorial puede tener muchos libros; cada libro pertenece a una editorial.
- Un lector puede tener muchos préstamos; cada préstamo pertenece a un lector.
- Un libro puede aparecer en muchos préstamos; cada préstamo corresponde a un libro.

El equipo debe definir los nombres de tablas, tipos de datos, claves primarias, claves foráneas, campos opcionales y restricciones de integridad.

## 5. Funcionalidades obligatorias

### 5.1. Autenticación

- Iniciar sesión con correo y contraseña, y cerrar sesión.
- Mostrar mensajes cuando las credenciales sean incorrectas.
- Proteger todas las rutas administrativas: no basta con ocultar los botones.
- Crear una cuenta de demostración mediante un seeder. No se requiere registro público.

### 5.2. Gestión CRUD

- Crear formularios para registrar y editar las seis entidades.
- Mostrar listados paginados y una vista de detalle por entidad.
- Incluir acciones visibles para consultar, editar y eliminar.
- Solicitar confirmación antes de eliminar.
- Mostrar mensajes de éxito y errores de validación junto al formulario.
- Conservar los datos válidos del formulario cuando falle una validación.
- Mostrar los nombres de las entidades relacionadas, no solamente sus identificadores.

### 5.3. Catálogo y detalle de libros

- Mostrar los libros en tarjetas con portada, título, autor, categoría y disponibilidad.
- Permitir abrir una ficha con descripción, editorial, ISBN y año de publicación.
- Mostrar una imagen predeterminada cuando el libro no tenga portada.
- Indicar cuántas unidades están disponibles para prestar.

### 5.4. Filtros y búsquedas

| Pantalla | Filtros mínimos |
| --- | --- |
| Catálogo y listado administrativo de libros | Texto por título o ISBN; categoría; autor; editorial; disponibilidad: todos, disponibles o sin disponibilidad. |
| Categorías | Búsqueda por nombre. |
| Autores | Búsqueda por nombre y filtro por nacionalidad. |
| Editoriales | Búsqueda por nombre y filtro por ciudad. |
| Lectores | Búsqueda por nombre o documento. |
| Préstamos | Lector, libro, estado y rango de fechas de préstamo. |

Los filtros deben ejecutarse sobre la base de datos y permitir combinar criterios. Ejemplo: consultar libros de tecnología de un autor específico que tengan disponibilidad.

Cada pantalla debe permitir limpiar los filtros, conservarlos al cambiar de página y mostrar un mensaje si no hay resultados. Los préstamos deben poder consultarse como **activos**, **devueltos** o **vencidos**; un vencido es un préstamo activo cuya fecha límite ya pasó.

### 5.5. Imágenes

- Permitir cargar la portada de un libro y la fotografía opcional de un autor desde archivos del equipo.
- Aceptar únicamente JPG, PNG o WebP, con un tamaño máximo de 2 MB por archivo.
- Validar el tipo real del archivo y su tamaño desde el servidor.
- Guardar los archivos mediante el almacenamiento de Laravel y conservar su ruta en la base de datos.
- Mostrar la imagen actual al editar y permitir reemplazarla.
- Si no se carga una imagen nueva, conservar la anterior. Al reemplazarla o eliminar el registro, retirar el archivo anterior cuando no sea compartido por otro registro.
- No exigir imágenes para todos los registros; usar una imagen predeterminada en su ausencia.

### 5.6. Préstamos y disponibilidad

- Registrar un préstamo seleccionando un lector y un libro existentes.
- Registrar la devolución con su fecha real.
- Consultar los préstamos de un lector y el historial de préstamos de un libro.
- Calcular la disponibilidad como: **cantidad total menos préstamos sin devolución**.
- Actualizar la disponibilidad al crear, editar, devolver o eliminar un préstamo.

La eliminación de un préstamo se permite para corregir registros de esta actividad y requiere confirmación. Si estaba activo, su eliminación libera una unidad; si ya estaba devuelto, no cambia la disponibilidad.

## 6. Reglas de negocio y validaciones

1. No se permiten ISBN ni documentos de lectores duplicados.
2. La cantidad total de libros debe ser un entero mayor o igual a cero.
3. No se puede prestar un libro sin unidades disponibles.
4. No se puede reducir la cantidad total de un libro por debajo de sus préstamos activos.
5. La fecha límite debe ser igual o posterior a la fecha del préstamo.
6. La fecha real de devolución no puede ser anterior a la fecha del préstamo ni estar en el futuro.
7. No se permiten fechas de préstamo futuras.
8. No se puede registrar una segunda devolución del mismo préstamo.
9. Al editar un préstamo y cambiar su libro, se debe recalcular la disponibilidad de ambos libros. Si el préstamo sigue activo, el nuevo libro debe tener disponibilidad.
10. Un préstamo devuelto no se puede reabrir desde la edición; sus correcciones deben conservar la devolución y fechas coherentes.
11. No se puede eliminar una categoría, autor o editorial que tenga libros asociados.
12. No se puede eliminar un libro o lector que tenga préstamos asociados, incluso si ya fueron devueltos.
13. Las restricciones de eliminación deben mostrar un mensaje comprensible y no un error técnico.
14. Todas las validaciones deben ejecutarse en el servidor; se pueden complementar desde la interfaz.
15. Las operaciones que afectan disponibilidad deben mantener la consistencia de los datos, incluso ante solicitudes simultáneas. El equipo debe explicar cómo evitó prestar más unidades de las existentes.

Los estados del préstamo pueden calcularse a partir de sus fechas. No es obligatorio guardarlos como un campo adicional.

## 7. Requisitos técnicos

- Desarrollar con Laravel y una base de datos relacional.
- Separar responsabilidades mediante rutas, controladores, modelos y vistas.
- Definir la estructura de la base de datos con migraciones y las relaciones con Eloquent.
- Utilizar Blade para las vistas; se permite Bootstrap o Tailwind para la presentación.
- Usar protección CSRF y los métodos HTTP correspondientes para las operaciones de escritura.
- Incluir seeders o factories para cargar datos de demostración.
- Mantener credenciales y secretos fuera del repositorio; entregar un `.env.example` sin información sensible.
- Presentar una interfaz navegable y adaptable a computador y celular.

## 8. Trabajo por etapas

### Etapa 1: análisis

Identificar actores, necesidades, funcionalidades y reglas. Elaborar el diagrama de casos de uso y explicar los flujos principales.

### Etapa 2: diseño de datos

Elaborar el diagrama entidad-relación de las seis entidades y la cuenta administrativa, con atributos, claves y cardinalidades. Revisar que el modelo permita cumplir las reglas antes de implementar las migraciones.

### Etapa 3: implementación

Construir la autenticación, los seis CRUD, el catálogo, los filtros, la gestión de imágenes y el flujo de préstamos y devoluciones.

### Etapa 4: validación y entrega

Cargar datos de prueba, verificar los escenarios de aceptación y actualizar los diagramas para que representen la aplicación entregada.

## 9. Entregables finales

### Entregable 1: diagrama de casos de uso

Debe incluir:

- Actores **visitante** y **administrador**.
- Límite del sistema y asociaciones entre actores y casos de uso.
- Consulta de catálogo, filtros y detalle de libros para el visitante.
- Inicio y cierre de sesión, CRUD de las seis entidades, registro de devolución y consulta de historiales para el administrador.
- Relaciones `include` o `extend` únicamente cuando correspondan; no es obligatorio utilizarlas.

Representar los casos de uso con notación UML. Se pueden agrupar por módulo para conservar la legibilidad, pero deben quedar identificadas las operaciones CRUD. Entregar una exportación legible en PDF, PNG o SVG y el archivo editable.

Acompañar el diagrama con una descripción breve de **registrar préstamo**, **registrar devolución** y **eliminar un registro con dependencias**: actor, precondiciones, flujo principal, excepciones y resultado esperado.

### Entregable 2: diagrama entidad-relación

Debe mostrar:

- Las seis entidades de negocio y la tabla de administradores utilizada para autenticación.
- Atributos, tipos de datos, claves primarias y foráneas.
- Cardinalidades, campos opcionales y restricciones de unicidad.
- Correspondencia con las migraciones y la base de datos implementada.

Las tablas técnicas del framework pueden omitirse si se aclara esa decisión. Entregar una exportación legible en PDF, PNG o SVG y el archivo editable.

### Entregable 3: aplicación funcionando

Entregar el repositorio o código fuente completo, con:

- Migraciones, seeders y recursos necesarios para ejecutar el proyecto.
- Un `README.md` con requisitos, instalación, configuración de la base de datos, migración, carga de datos, configuración del almacenamiento de imágenes y ejecución.
- Credenciales de una cuenta de demostración local.
- Instrucciones para reproducir los escenarios de aceptación.

La aplicación debe ejecutarse desde una instalación limpia siguiendo el README. Puede demostrarse localmente; no se exige publicarla en internet.

## 10. Datos mínimos de demostración

| Información | Cantidad mínima |
| --- | --- |
| Categorías | 5 |
| Autores | 8 |
| Editoriales | 4 |
| Libros | 20, al menos 10 con portada |
| Lectores | 10 |
| Préstamos | 15: 5 activos dentro del plazo, 5 activos vencidos y 5 devueltos |

Distribuir los préstamos para respetar las existencias. Incluir al menos un libro con todas sus unidades prestadas y otro con unidades disponibles. Los datos deben permitir probar filtros combinados y paginación.

## 11. Escenarios de aceptación

| Escenario | Resultado esperado |
| --- | --- |
| Un visitante intenta abrir una ruta administrativa | El sistema solicita iniciar sesión. |
| El administrador crea, consulta, edita y elimina un registro sin dependencias de cada entidad | Las operaciones se reflejan en la base de datos y en la interfaz. |
| Se intenta registrar un ISBN o documento duplicado | Se informa la validación y se conserva la información válida del formulario. |
| Se carga una portada válida y luego se reemplaza | Se muestra la nueva imagen y se retira el archivo anterior. |
| Se carga un archivo no permitido o mayor de 2 MB | Se rechaza el archivo y se explica el motivo. |
| Se combinan categoría, autor y disponibilidad | Solo aparecen libros que cumplen todos los criterios. |
| Se cambia de página con filtros activos | Los filtros se mantienen. |
| Se registra un préstamo de la última unidad disponible | La disponibilidad queda en cero y se impide otro préstamo activo. |
| Se registra la devolución | El préstamo aparece como devuelto y la disponibilidad aumenta en uno. |
| Se cambia el libro de un préstamo activo | La unidad del libro anterior se libera y se ocupa una del nuevo libro. |
| Se elimina un préstamo activo | Se libera la unidad correspondiente. |
| Se intenta eliminar una categoría con libros | Se bloquea la eliminación y se informa la dependencia. |
| Se intenta reducir existencias por debajo de los préstamos activos | Se rechaza el cambio. |
| Dos solicitudes intentan prestar la última unidad simultáneamente | Solo una consigue registrar el préstamo. |
| Se instala el proyecto siguiendo el README | La aplicación inicia con datos e imágenes de demostración disponibles. |


**Resultado esperado:** un sistema que permita consultar el catálogo y administrar la biblioteca de principio a fin, acompañado de diagramas que expliquen sus funcionalidades y su estructura de datos.

> **Nota para Cami**
>
> Este proyecto es una oportunidad para transformar el conocimiento en una solución concreta. Que cada reto sea una ocasión para aprender y fortalecer sus habilidades.