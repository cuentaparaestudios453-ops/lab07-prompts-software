# Biblioteca de prompts — Laboratorio 07

## Tarea 1 — Clasificación de issues

**Herramienta:** Gemini

### Versión A — Sin ejemplos (zero-shot)

**Prompt utilizado:**
>
    Clasifica cada issue de GitHub indicando su tipo, prioridad y módulo.

1. "Al pagar con tarjeta aparece error 500 y el pedido queda duplicado."
2. "Agregar modo oscuro en la pantalla de perfil."
3. "¿El endpoint /api/pedidos acepta paginación?"
4. "El login tarda 8 segundos cuando hay más de 100 usuarios conectados."
5. "Actualizar la librería axios por una alerta de seguridad."

**Resultado:**

1. **Tipo:** Bug (Error / Incidencia) | **Prioridad:** Alta / Crítica | **Módulo:** Pagos / Checkout.
2. **Tipo:** Feature (Nueva funcionalidad / Mejora) | **Prioridad:** Baja / Media | **Módulo:** Interfaz de usuario (UI) / Perfil.
3. **Tipo:** Question (Pregunta / Consulta técnica) | **Prioridad:** Baja | **Módulo:** API / Backend (Pedidos).
4. **Tipo:** Bug / Performance (Problema de rendimiento) | **Prioridad:** Alta | **Módulo:** Autenticación / Login.
5. **Tipo:** Chore / Maintenance (Mantenimiento / Seguridad) | **Prioridad:** Media / Alta | **Módulo:** Dependencias / Seguridad.

**Evaluación:** La respuesta identificó el problema principal de cada issue y explicó sus prioridades. Sin embargo, utilizó distintos nombres para los tipos, ofreció varias prioridades posibles y mencionó más de un módulo en algunos casos.

### Versión B — Few-shot (3 ejemplos)

**Prompt utilizado:**
>
    Clasifica cada issue siguiendo exactamente el formato de los ejemplos.

    Tipos válidos: BUG, FEATURE, CONSULTA, RENDIMIENTO, SEGURIDAD.

    Prioridad: ALTA (afecta dinero, datos o seguridad), MEDIA o BAJA.

    **Ejemplos:**

    - Issue: "La app se cierra al subir una foto de perfil."
    Tipo: BUG | Prioridad: MEDIA | Módulo: perfil

    - Issue: "Permitir exportar el historial de pedidos a Excel."
    Tipo: FEATURE | Prioridad: BAJA | Módulo: pedidos

    - Issue: "Las contraseñas se guardan en texto plano en la tabla usuarios."
    Tipo: SEGURIDAD | Prioridad: ALTA | Módulo: autenticación

    **Issues a clasificar:** Se utilizaron los mismos cinco issues de la versión A.

**Resultado:**

1. Tipo: BUG | Prioridad: ALTA | Módulo: pagos
2. Tipo: FEATURE | Prioridad: BAJA | Módulo: perfil
3. Tipo: CONSULTA | Prioridad: BAJA | Módulo: pedidos
4. Tipo: RENDIMIENTO | Prioridad: MEDIA | Módulo: autenticación
5. Tipo: SEGURIDAD | Prioridad: ALTA | Módulo: dependencias

**Mejora observada:**

La versión B utilizó únicamente los tipos permitidos y mantuvo el mismo formato en los cinco issues. También asignó una sola prioridad y un módulo específico a cada caso. En cambio, la versión A utilizó categorías diferentes y presentó prioridades alternativas. Los ejemplos ayudaron a obtener una respuesta más ordenada y consistente.

## Tarea 2 — Razonamiento lógico

**Herramienta:** Gemini

### Versión A — Sin ejemplos (zero-shot)

**Prompt utilizado:**
>
    Un sistema tiene 7 microservicios que se despliegan por rondas.

    Un servicio solo se despliega cuando todas sus dependencias ya están desplegadas. Los servicios sin dependencias pendientes se despliegan en paralelo en la misma ronda.

    - usuarios depende de auth
    - pagos depende de usuarios
    - pedidos depende de usuarios, pagos y catalogo
    - notificaciones depende de pedidos
    - gateway depende de auth y notificaciones
    - auth y catalogo no tienen dependencias

    ¿Cuántas rondas se necesitan como mínimo? Responde solo con el número.

**Resultado:**

6

**Evaluación:** La respuesta fue correcta, pero no mostró cómo se organizaron los servicios en cada ronda, por lo que fue necesario verificar el resultado manualmente.

### Versión B — Few-shot con razonamiento paso a paso

**Prompt utilizado:**
>
    Resuelve el problema razonando ronda por ronda, igual que en los ejemplos.

    **Ejemplo 1:**

    Servicios: A, B. B depende de A.

    Razonamiento: Ronda 1: A (sin dependencias). Ronda 2: B (A ya está desplegado).

    Respuesta: 2 rondas.

    **Ejemplo 2:**

    Servicios: A, B, C. C depende de A y B.

    Razonamiento: Ronda 1: A y B en paralelo. Ronda 2: C.

    Respuesta: 2 rondas.

    **Ejemplo 3:**

    Servicios: A, B, C, D. B y C dependen de A. D depende de B y C.

    Razonamiento: Ronda 1: A. Ronda 2: B y C en paralelo. Ronda 3: D.

    Respuesta: 3 rondas.

    **Problema:** Se utilizó el mismo sistema de microservicios de la versión A, solicitando el razonamiento ronda por ronda.

**Resultado:**

- **Ronda 1:** `auth` y `catalogo`, porque no tienen dependencias.
- **Ronda 2:** `usuarios`, porque `auth` ya está desplegado.
- **Ronda 3:** `pagos`, porque `usuarios` ya está desplegado.
- **Ronda 4:** `pedidos`, porque `usuarios`, `pagos` y `catalogo` ya están desplegados.
- **Ronda 5:** `notificaciones`, porque `pedidos` ya está desplegado.
- **Ronda 6:** `gateway`, porque `auth` y `notificaciones` ya están desplegados.

**Respuesta:** 6 rondas.

**Verificación manual:**

Ambas versiones coinciden en que se necesitan 6 rondas. La respuesta se verificó revisando las dependencias de cada servicio y comprobando que ninguno se despliegue antes de que sus dependencias estén listas.

**Prueba de ciclo:**

Se agregó la dependencia `catalogo depende de gateway`.

Esto genera un ciclo entre `catalogo`, `pedidos`, `notificaciones` y `gateway`. Como `catalogo` depende de `gateway`, `gateway` depende de `notificaciones`, `notificaciones` depende de `pedidos` y `pedidos` depende de `catalogo`, ninguno de esos cuatro servicios puede desplegarse primero. Por lo tanto, no es posible desplegar todos los servicios mientras se mantenga esa dependencia.

**Mejora observada:**

La versión A dio la respuesta correcta de forma breve, pero no permitió ver el procedimiento utilizado. La versión B explicó cada ronda y facilitó la verificación de las dependencias. Por eso, con few-shot fue más sencillo comprobar el resultado y detectar posibles errores.

## Tarea 3 — Extracción estructurada

**Herramienta:** Gemini

### Versión A — Sin ejemplos (zero-shot)

**Prompt utilizado:**
>
    Extrae los datos importantes de estos reportes de error en formato JSON.

    Reporte 1: "Desde ayer, en la versión 2.3.1 de Android, cuando un cliente aplica el cupón BIENVENIDA20 el total no se actualiza. Lo reportó Ana Díaz de soporte."

    Reporte 2: "En la web el botón Descargar factura no hace nada y no muestra ningún error. Reportado por Luis Paredes el 3 de octubre."

**Resultado:**

```json
[
  {
    "id": 1,
    "plataforma": "Android",
    "version": "2.3.1",
    "fecha_inicio": "ayer",
    "problema": "El total no se actualiza al aplicar el cupón.",
    "detalles_adicionales": "Cupón: BIENVENIDA20",
    "reportado_por": "Ana Díaz (soporte)"
  },
  {
    "id": 2,
    "plataforma": "Web",
    "version": null,
    "fecha_reporte": "3 de octubre",
    "problema": "El botón 'Descargar factura' no hace nada y no muestra ningún error.",
    "detalles_adicionales": null,
    "reportado_por": "Luis Paredes"
  }
]
```

**Evaluación:** El resultado tiene una estructura JSON válida y permite identificar los datos importantes de ambos reportes. Sin embargo, utiliza nombres de campos diferentes y no mantiene el mismo esquema para las fechas.

### Versión B — Few-shot (3 ejemplos)

**Prompt utilizado:**
>
    Convierte cada reporte en un objeto JSON con el mismo esquema de los ejemplos. Si un dato no aparece, usa `null`. Devuelve solo un arreglo JSON válido.

**Ejemplos utilizados:**

1. Reporte de un error en iOS al abrir el mapa.
2. Reporte de un problema en el formulario de registro web.
3. Reporte de notificaciones duplicadas en Android.

>
    # Ejemplo 1
    Reporte: "En iOS, versión 2.2.0, la app se congela al abrir el mapa. Reportó Carla Soto."
    {"plataforma": "iOS", "version_app": "2.2.0", "modulo": "mapa", "sintoma": "la app se congela", "reportado_por": "Carla Soto", "fecha": null}

    # Ejemplo 2
    Reporte: "El 12 de septiembre, en la web, el formulario de registro acepta correos sin @."
    {"plataforma": "web", "version_app": null, "modulo": "registro", "sintoma": "acepta correos sin @", "reportado_por": null, "fecha": "12 de septiembre"}

    # Ejemplo 3
    Reporte: "Jorge Ruiz indica que en Android 2.3.0 las notificaciones llegan duplicadas."
    {"plataforma": "Android", "version_app": "2.3.0", "modulo": "notificaciones", "sintoma": "notificaciones duplicadas", "reportado_por": "Jorge Ruiz", "fecha": null}


**Resultado:**

```json
[
  {
    "plataforma": "Android",
    "version_app": "2.3.1",
    "modulo": "cupones",
    "sintoma": "el total no se actualiza",
    "reportado_por": "Ana Díaz",
    "fecha": "ayer"
  },
  {
    "plataforma": "web",
    "version_app": null,
    "modulo": "facturación",
    "sintoma": "el botón Descargar factura no hace nada y no muestra ningún error",
    "reportado_por": "Luis Paredes",
    "fecha": "3 de octubre"
  }
]
```

**Validación del JSON:**

- **¿El JSON es válido?** Sí, presenta un arreglo con dos objetos y una sintaxis JSON correcta.
- **¿Se mantienen los nombres de los campos?** Sí, ambos objetos utilizan los mismos seis campos de los ejemplos.
- **¿Usó `null` para los datos faltantes?** Sí, utilizó `null` para la versión de la aplicación del segundo reporte.
- **¿Cómo trató la fecha "desde ayer"?** Conservó la expresión como `"ayer"`, sin convertirla en una fecha exacta.

**Mejora observada:**

La versión A extrajo los datos importantes, pero utilizó nombres de campos diferentes. La versión B mantuvo el esquema solicitado y utilizó `null` para los datos faltantes. Esto permitió obtener una respuesta más ordenada, consistente y fácil de procesar.

## Tarea 4 — Revisión de código

**Herramienta:** Gemini

### Versión A — Sin rol

**Prompt utilizado:**

Revisa este código:

```python
def login(usuario, clave):
    query = "SELECT * FROM usuarios WHERE nombre='" + usuario + "' AND clave='" + clave + "'"
    return db.execute(query).fetchone() is not None
```

**Resultado:**

Gemini identificó una vulnerabilidad crítica de inyección SQL, causada por la concatenación directa de los datos ingresados por el usuario. Explicó que un atacante podría modificar la consulta e intentar acceder sin conocer la contraseña.

También recomendó utilizar consultas parametrizadas y señaló el riesgo de almacenar contraseñas en texto plano. Finalmente, presentó un ejemplo de código corregido y mencionó que la sintaxis de los parámetros depende de la biblioteca de base de datos utilizada.

**Evaluación:** La respuesta identificó el problema principal y propuso una solución. Sin embargo, presentó las recomendaciones de forma general y no utilizó una tabla para organizar las vulnerabilidades, los riesgos y sus correcciones.

### Versión B — Role prompting y prompts estructurados con XML

**Prompt utilizado:**

```xml
<rol>
Eres un revisor de seguridad de aplicaciones web con experiencia en OWASP Top 10.
</rol>
<contexto>
Función de login de una API en Python con SQLite. El equipo de desarrollo es junior.
</contexto>
<codigo>
def login(usuario, clave):
    query = "SELECT * FROM usuarios WHERE nombre='" + usuario + "' AND clave='" + clave + "'"
    return db.execute(query).fetchone() is not None
</codigo>
<tarea>
1. Identifica las vulnerabilidades y explica el riesgo de cada una.
2. Propón el código corregido usando consultas parametrizadas.
</tarea>
<formato>
Tabla Markdown: Vulnerabilidad | Riesgo | Corrección. Luego, el código corregido.
</formato>
```

**Resultado:**

Gemini identificó dos riesgos principales:

| Vulnerabilidad | Riesgo | Corrección |
|---|---|---|
| Inyección SQL (SQLi) | Un atacante podría manipular la consulta e intentar evadir la autenticación o acceder a información no autorizada. | Utilizar consultas parametrizadas con marcadores `?` en SQLite. |
| Almacenamiento y comparación insegura de contraseñas | Si las contraseñas se guardan en texto plano, una filtración de la base de datos podría exponer las credenciales. El código no permite confirmar cómo se almacenan actualmente. | Guardar hashes de contraseñas utilizando algoritmos seguros como bcrypt o Argon2 y verificar las contraseñas mediante la función correspondiente. |

**Código corregido propuesto por Gemini:**

```python
import bcrypt

def login(usuario, clave):
    query = "SELECT clave_hash FROM usuarios WHERE nombre = ?"
    resultado = db.execute(query, (usuario,)).fetchone()

    if resultado is None:
        return False

    stored_hash = resultado[0]
    return bcrypt.checkpw(
        clave.encode("utf-8"),
        stored_hash.encode("utf-8")
    )
```

Este ejemplo supone que la columna `clave_hash` existe y que las contraseñas ya están almacenadas como hashes compatibles con bcrypt.

**Mejora observada:**

La versión A explicó la inyección SQL y recomendó una solución, pero presentó la información de forma general. La versión B, al incluir un rol especializado, un contexto y etiquetas XML, organizó los riesgos en una tabla y propuso código adaptado a SQLite. Además, identificó la importancia de proteger las contraseñas mediante hashes seguros.

La técnica de role prompting y el formato estructurado hicieron que la respuesta fuera más específica, ordenada y fácil de revisar.

**Comparación entre ambas versiones:**

La versión A identificó la inyección SQL y recomendó utilizar consultas parametrizadas. También mencionó el riesgo de almacenar contraseñas en texto plano y presentó un ejemplo de código corregido.

En la versión B, Gemini organizó las vulnerabilidades en una tabla, explicó los riesgos y propuso una solución adaptada a SQLite. El rol de revisor de seguridad y las etiquetas XML ayudaron a definir mejor el contexto, las tareas y el formato esperado.

La principal mejora fue la organización y especificidad de la respuesta. Además, se destacó la importancia de almacenar las contraseñas mediante hashes seguros. Sin embargo, el código original no permite confirmar si las contraseñas realmente se guardan en texto plano; ese aspecto debe verificarse en la implementación.

## Rúbrica de evaluación

Cada versión se califica del 1 al 5 en cada criterio. El puntaje se asigna según la revisión de los resultados obtenidos.

| Criterio | Descripción | Puntaje (1-5) |
|---|---|:---:|
| Exactitud | La respuesta es correcta y fue verificada. |  |
| Formato | Respeta el formato o esquema solicitado. |  |
| Consistencia | Aplica el mismo criterio en todos los casos. |  |
| Utilidad | Se puede utilizar directamente en el proyecto. |  |

## Comparación: sin ejemplos vs. técnica avanzada

| Tarea | Versión A | Versión B | Mejora principal |
|---|:---:|:---:|---|
| 1. Clasificación de issues | 13/20 | 20/20 | Se utilizaron únicamente los tipos permitidos, con prioridades claras y un formato uniforme. |
| 2. Razonamiento lógico | 17/20 | 20/20 | Se explicó el orden de despliegue por rondas y se facilitó la verificación de las dependencias. |
| 3. Extracción estructurada | 14/20 | 20/20 | Se respetaron los campos establecidos, se utilizó `null` para datos ausentes y se mantuvo un JSON uniforme. |
| 4. Revisión de código (rol) | 16/20 | 20/20 | Se organizaron las vulnerabilidades en una tabla y se propuso código corregido para SQLite con consultas parametrizadas. |

### Evaluación de los resultados

Los puntajes son una estimación basada en las respuestas obtenidas. La versión B tuvo mejores resultados porque las instrucciones más específicas ayudaron a mantener el formato, la consistencia y la utilidad de las respuestas. Sin embargo, también fue necesario revisar manualmente la exactitud del contenido y comprobar que las soluciones fueran adecuadas para cada caso.

### Puntajes por criterio

| Tarea | Versión | Exactitud | Formato | Consistencia | Utilidad | Total |
|---|---|:---:|:---:|:---:|:---:|:---:|
| Clasificación de issues | A | 3 | 3 | 3 | 4 | 13/20 |
| Clasificación de issues | B | 5 | 5 | 5 | 5 | 20/20 |
| Razonamiento lógico | A | 5 | 3 | 4 | 5 | 17/20 |
| Razonamiento lógico | B | 5 | 5 | 5 | 5 | 20/20 |
| Extracción estructurada | A | 4 | 2 | 4 | 4 | 14/20 |
| Extracción estructurada | B | 5 | 5 | 5 | 5 | 20/20 |
| Revisión de código | A | 4 | 3 | 4 | 5 | 16/20 |
| Revisión de código | B | 5 | 5 | 5 | 5 | 20/20 |

## FASE 6 — Biblioteca de prompts y publicación

### Plantilla 1: Clasificación few-shot

```text
Actúa como {{rol}}.

Clasifica cada {{elemento}} siguiendo el formato de los ejemplos.
Utiliza únicamente estas categorías: {{categorias}}.
Asigna la prioridad según estos criterios: {{criterios_prioridad}}.

Ejemplo 1:
Entrada: {{entrada_1}}
Salida: {{salida_1}}

Ejemplo 2:
Entrada: {{entrada_2}}
Salida: {{salida_2}}

Ejemplo 3:
Entrada: {{entrada_3}}
Salida: {{salida_3}}

Elementos a clasificar:
{{datos}}

Devuelve los resultados con el formato:
Tipo | Prioridad | Módulo

No agregues categorías fuera de las permitidas.
```

### Plantilla 2: Razonamiento lógico few-shot

```text
Actúa como {{rol}} y resuelve el siguiente problema de dependencias.

Sigue el método de los ejemplos: identifica los elementos disponibles,
verifica sus dependencias y organiza la solución por rondas.
En cada ronda, incluye únicamente los elementos cuyas dependencias
ya se hayan completado.

Ejemplo 1:
Problema: {{problema_1}}
Solución por rondas: {{solucion_1}}

Ejemplo 2:
Problema: {{problema_2}}
Solución por rondas: {{solucion_2}}

Ejemplo 3:
Problema: {{problema_3}}
Solución por rondas: {{solucion_3}}

Problema a resolver:
{{problema}}

Presenta las rondas en orden y explica brevemente por qué se puede
procesar cada grupo. Si existe un ciclo de dependencias, identifícalo
e indica si es posible completar el proceso.
```

### Plantilla 3: Extracción estructurada a JSON few-shot

```text
Actúa como {{rol}} y extrae la información de los textos proporcionados.

Utiliza exactamente estos campos:
{{campos}}

Ejemplo 1:
Texto: {{texto_1}}
JSON: {{json_1}}

Ejemplo 2:
Texto: {{texto_2}}
JSON: {{json_2}}

Ejemplo 3:
Texto: {{texto_3}}
JSON: {{json_3}}

Textos a procesar:
{{textos}}

Devuelve un arreglo JSON válido.
Respeta los nombres y tipos de los campos establecidos en los ejemplos.
Si un dato no aparece en el texto, utiliza null.
No inventes información ni agregues campos adicionales.
No incluyas explicaciones fuera del JSON.
```

### Verificación de las plantillas

Las tres plantillas permiten reutilizar las técnicas de few-shot prompting en diferentes situaciones. Solo es necesario reemplazar las variables entre llaves dobles por el rol, los ejemplos y los datos del nuevo problema.
