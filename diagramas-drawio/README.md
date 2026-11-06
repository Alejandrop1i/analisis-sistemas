# Taller · Diagramas con draw.io · RPG Manager

**Estudiante:** Oscar Iván Ramírez  
**Fecha:** 5 de noviembre de 2026  
**Herramienta:** draw.io (app.diagrams.net), exportado a SVG con copia del diagrama incluida

Los tres diagramas describen el mismo sistema, el RPG Manager, a tres distancias:
el contexto muestra con quién intercambia datos, los casos de uso muestran qué
puede lograr cada actor y el de actividades muestra cómo ocurre paso a paso un
caso de uso concreto.

| Archivo | Contenido |
|---|---|
| `01_contexto_rpg.drawio` / `.svg` | DFD nivel 0 con los flujos F1 a F11 |
| `02_casos_uso_rpg.drawio` / `.svg` | 13 casos de uso, 4 actores, include, extend y generalización |
| `03_actividades_combate.drawio` | Dos páginas: CU-07 Combatir en la Arena y CU-03 Crear Personaje |
| `03_actividades_cu07.svg` | Página 1 exportada |
| `03_actividades_cu03.svg` | Página 2 exportada |

---

## Bloque 1 · Diagrama de contexto

![Diagrama de contexto](01_contexto_rpg.svg)

### Ejercicio 1A · Flujos que faltaban

| # | Origen | Flujo de datos | Destino |
|---|---|---|---|
| F10 | Jugador | credenciales de acceso (usuario y contraseña) | RPG Manager |
| F11 | Servicio de Respaldo en la Nube | partida restaurada | RPG Manager |

Para F10 el dato que viaja al iniciar sesión no es "iniciar sesión" (eso sería una
acción) sino las credenciales con las que el sistema decide si deja entrar o no.
Para F11 la dirección es de vuelta: el respaldo que salió en F9 regresa como
partida restaurada cuando el jugador la pide, así que la flecha apunta hacia el
RPG Manager.

### Bug a corregir · Contexto

- **Error 1.** "Base de datos de Personajes" no es una entidad externa. Es un
  almacén que vive dentro del sistema y lo controlamos nosotros. En el nivel 0 el
  sistema es una caja negra y los almacenes aparecen recién en el DFD de nivel 1.
- **Error 2.** La flecha "Crear Personaje" está nombrada con una acción. Un flujo
  de datos se nombra con lo que viaja, por ejemplo "datos del personaje". Crear
  Personaje es un caso de uso y pertenece al diagrama del Bloque 2.
- **Error 3.** La flecha directa de Jugador a Game Master conecta dos entidades
  externas entre sí. En un diagrama de contexto todo intercambio pasa por el
  proceso 0. Si el jugador le manda un mensaje al Game Master, lo hace a través
  del RPG Manager.

### Checklist

- [x] Un solo proceso numerado 0.
- [x] Cuatro entidades externas: Jugador, Game Master, Servicio de Correo, Servicio de Respaldo en la Nube.
- [x] Once flujos, todos nombrados con sustantivos.
- [x] Ninguna flecha une dos entidades externas.
- [x] No hay bases de datos.

---

## Bloque 2 · Diagrama de casos de uso

![Diagrama de casos de uso](02_casos_uso_rpg.svg)

### Ejercicio 2A · Generalización de actores

**¿Hacia dónde apunta el triángulo vacío y por qué?**
Apunta hacia Jugador, el actor general. La generalización se lee como "Jugador
Premium es un tipo de Jugador", y en UML la flecha siempre sale del caso
particular y señala al general, igual que una clase hija apunta a su clase padre
en el diagrama de clases.

**¿Por qué el Jugador Premium no necesita líneas a CU-01 … CU-10?**
Porque las hereda. Al ser una especialización de Jugador, el Premium participa
automáticamente en todo lo que el Jugador puede hacer: registrarse, iniciar
sesión, crear personaje, combatir, etc. Dibujar esas diez líneas otra vez sería
información repetida y haría ilegible el diagrama. Solo se conecta de forma
directa a CU-13 Comprar ítem exclusivo, que es lo único que lo diferencia.

### Bug a corregir · Casos de uso

- **Error 1.** "Inventario" es un sustantivo suelto, no un objetivo del actor. Un
  caso de uso se nombra con verbo más sustantivo: "Gestionar Inventario".
- **Error 2.** El Game Master está dentro del rectángulo del sistema. Los actores
  son externos por definición, así que van fuera del límite y se conectan con
  líneas a los casos que usan.
- **Error 3.** La flecha include está invertida. El include sale del caso base y
  apunta al incluido: CU-03 Crear Personaje → CU-04 Elegir clase, porque crear un
  personaje siempre obliga a elegir clase, no al revés.
- **Error 4.** Dos actores no se asocian entre sí con una línea. Los actores solo
  se conectan a casos de uso, o entre ellos mediante generalización cuando uno es
  un tipo especial del otro. Jugador y Game Master son roles distintos, no hay
  relación directa.

---

## Bloque 3 · Diagrama de actividades

### CU-07 Combatir en la Arena

![Actividades CU-07](03_actividades_cu07.svg)

### Ejercicio 3A · ¿Y si se acaban las pociones?

**¿En qué carril va la decisión?** En el carril RPG Manager. Quien sabe cuántas
pociones hay en el inventario es el sistema, no el jugador. El jugador solo
expresa la intención de usar una; comprobarla es responsabilidad del sistema.

| Guarda | Acción a la que va |
|---|---|
| `[pociones > 0]` | Restaurar vida del Personaje |
| `[pociones == 0]` | Informar "Sin pociones" y regresar a la decisión ¿acción? |

Decidí que el intento fallido no consuma el turno: el sistema avisa y devuelve el
control al jugador para que elija de nuevo. Perder el turno por un clic a ciegas
sería castigar al jugador por algo que el sistema pudo decirle antes.

### CU-03 Crear Personaje (Ejercicio 3B)

![Actividades CU-03](03_actividades_cu03.svg)

**¿Qué caso de uso del Bloque 2 aparece "escondido" dentro de este flujo?**
CU-04 Elegir clase. En el diagrama de casos de uso aparece como un caso incluido
por CU-03; en el diagrama de actividades se convierte en la acción "Elegir clase
del personaje" más la decisión ¿clase? con sus tres salidas. También asoma
CU-05 Gestionar Inventario: la acción "Crear Inventario vacío del Personaje" es
el punto donde nace el inventario que ese caso de uso administrará después.

### Bug a corregir · Actividades

- **Error 1.** El rombo "¿Tiene flechas?" tiene una sola salida `[sí]`. Toda
  decisión necesita guardas que cubran todos los casos; falta la salida `[no]`,
  y sin ella el flujo se queda sin camino cuando el personaje no tiene flechas.
- **Error 2.** El fork abre dos caminos paralelos que terminan cada uno en su
  propio nodo final. Los caminos de un fork deben reunirse en un join antes de
  seguir, porque el join es lo que garantiza que ambos terminaron. Con dos
  finales, el diagrama dice que la actividad acaba cuando termina cualquiera de
  los dos, lo cual es falso.
- **Error 3.** "Calcular daño al oponente" está en el carril del Jugador. Esa
  operación la ejecuta el sistema con las reglas del juego; el jugador solo elige
  atacar. En el carril equivocado, el diagrama miente sobre quién es responsable.

---

## Bloque 4 · Exportación y GitHub

Cada diagrama se exportó a SVG con la opción "Include a copy of my diagram", así
que cualquiera de los `.svg` se puede volver a abrir en draw.io y editar. Los
`.drawio` se conservan como fuente.

**URL de mi commit:** _(se completa al subir el repositorio)_

---

## Reflexión final

1. **¿Por qué el diagrama de contexto no muestra la base de datos si el sistema
   sí la usa?** Porque el contexto responde una sola pregunta: con quién
   intercambia datos el sistema. La base de datos no es alguien de afuera, es una
   pieza interna de la caja negra. Si la dibujo, estoy abriendo la caja, y eso
   corresponde al DFD de nivel 1.

2. **¿Cuál es la diferencia entre el flujo "datos del personaje" y el caso de uso
   "Crear Personaje"?** El flujo es la cosa que viaja: nombre y clase cruzando la
   frontera del sistema. El caso de uso es el objetivo que el jugador logra con
   esos datos. Uno es un sustantivo, el otro es un verbo. El mismo flujo puede
   servir a varios casos de uso, por ejemplo "datos del personaje" también viaja
   al editar la ficha.

3. **Ejemplo propio de include y de extend.** Include: CU-11 Crear Misión
   «include» Validar recompensa, porque el Game Master nunca puede guardar una
   misión sin que el sistema compruebe que la recompensa está dentro del rango
   permitido. Extend: Equipar objeto obtenido «extend» CU-06 Unirse a Misión,
   porque solo ocurre si la misión entregó un objeto y el jugador decide ponérselo
   en ese momento.

4. **¿Qué información aporta el diagrama de actividades que el de casos de uso no
   puede mostrar?** El orden, las decisiones y quién hace cada paso. El de casos
   de uso dice que el jugador puede combatir; el de actividades dice que primero
   se valida que esté vivo, que cada ronda tiene una decisión entre atacar o curar,
   que el ranking y la recompensa se actualizan en paralelo, y que todo eso lo
   ejecuta el sistema mientras el jugador solo elige y mira el resultado.

5. **"Calcular daño al oponente" será un método en POO. ¿En qué clase vive?**
   Lo pondría en una clase `Combate` (o `Arena`), no en `Personaje`. El daño
   depende de dos personajes a la vez, del atacante y del defensor, además de las
   reglas del turno. Si lo meto en `Personaje`, un personaje tendría que conocer
   los atributos internos del otro, lo cual rompe el encapsulamiento. `Combate`
   ya conoce a ambos y es quien ejecuta la ronda, así que el método le pertenece
   de forma natural.

6. **¿Cuándo preferiría PlantUML y cuándo draw.io?** PlantUML cuando el diagrama
   va a vivir junto al código y cambiar seguido, por ejemplo un diagrama de clases
   que debe mantenerse al día con cada refactorización; al ser texto, el diff en
   Git muestra exactamente qué cambió y se puede regenerar en CI. draw.io cuando
   el diagrama es para explicar algo a personas, por ejemplo este contexto para
   un stakeholder o un flujo de combate para el equipo de diseño, porque ahí
   importa controlar la posición, el color y el énfasis visual.

## Autoevaluación

| Tema | Nivel |
|---|---|
| Manejo básico de draw.io | ¡Lo tengo! |
| Diagrama de contexto y flujos de datos | ¡Lo tengo! |
| Actores y casos de uso | ¡Lo tengo! |
| «include», «extend» y generalización | ¡Lo tengo! |
| Decisiones, guardas y carriles | ¡Lo tengo! |
| Fork / Join | Más o menos |
| Exportar y versionar en GitHub | ¡Lo tengo! |
