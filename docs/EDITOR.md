# Editor del sitio (acceso de Víctor)

Es el mismo editor que el de [organization-deportiva-](https://github.com/andreasssbh/organization-deportiva-),
adaptado a este blog. El Dr. Víctor Jiménez puede cambiar textos, fotos y entradas **desde el propio sitio**.
Cada «Guardar» crea un commit en GitHub y Vercel vuelve a publicar la web en uno o dos minutos.

## Cómo lo usa Víctor

1. Entra en la web y pulsa **«Acceso editor»** (al final de la página), o abre directamente `/#acceso`.
2. Escribe su contraseña. Aparece una barra negra abajo: **Modo edición**. En este modo se ven todas las entradas
   y páginas seguidas (portada, entradas, «Acerca de», «Contacto»), cada una en un recuadro; con **«Ver»** la web
   vuelve a verse como un blog normal.
3. Hace clic en cualquier texto y escribe. La barra pequeña tiene las mismas herramientas que en el sitio anterior:
   negrita, cursiva, subrayado, tamaño, color, alineación, listas, enlaces, quitar formato, arrastrar, mover,
   cortar/pegar, duplicar, eliminar y añadir.
4. **Ficha de cada entrada** (recuadro amarillo arriba de cada entrada, solo visible en modo edición):
   `sección | fecha | temas | Borrador`, por ejemplo `Casos | 2026-11-04 | Gobernanza, Unión Europea | Borrador`.
   - Sección: `Análisis`, `Casos` o `Lecturas` (también vale en inglés).
   - Fecha `AAAA-MM-DD` (o `DD/MM/AAAA`): ordena el blog, la más reciente primero.
   - Temas separados por comas: son las etiquetas. Un tema nuevo aparece solo.
   - `Borrador` al final muestra la etiqueta «Borrador»; se borra para quitarla.
5. **Entrada nueva**: «＋ Entrada nueva aquí» (entre entradas), el panel «Añadir» o «＋ Añadir» en la barra.
   Sale con textos provisionales en los dos idiomas y la fecha de hoy. **«🗑 Eliminar esta entrada»** al final de
   cada una la quita (las páginas fijas —portada, Acerca de, Contacto— no se pueden eliminar).
6. Fotos: «Foto» en el menú «Añadir» y luego **«Cambiar foto»** (se reducen solas a 1800 px). PDF: en un enlace o
   botón, «Adónde lleva → Un PDF» sube el archivo (máximo 3 MB) a `documentos/`.
7. Cada **texto** existe en español y en inglés y se cambia en cada idioma por separado (botones ES / EN).
8. **Deshacer / Rehacer** en la barra (o Ctrl/⌘ + Z). **Guardar** publica; **Historial → Restaurar** vuelve a una
   versión anterior.

La sesión dura 12 horas. **Salir** la cierra.

## Qué se puede editar

Solo lo que está entre los comentarios `editable:main` (portada, entradas, Acerca de, Contacto) y `editable:footer`
(pie) de `index.html`. La cabecera, el menú, la barra lateral, los estilos y el script quedan fuera: el editor no
puede romper el diseño ni bloquearse el acceso. La barra lateral, las listas por sección/tema, la búsqueda, las
etiquetas, «Anterior/Siguiente» y «Relacionadas» se generan solas a partir de las entradas y sus fichas.

**Importante para nosotros**: Víctor guarda en `main`. Antes de tocar `index.html`, hacer `git pull` para no pisar
sus cambios, y no quitar ni duplicar los comentarios `editable:…`.

## Cómo funciona

| Pieza | Qué hace |
|---|---|
| `admin/editor.js` | El editor. `index.html` solo lo carga si existe la cookie `sm_editor` o se abre `#acceso`. |
| `api/login.js`, `api/logout.js`, `api/session.js` | Sesión: cookie firmada (HMAC, `HttpOnly`, `SameSite=Strict`, 12 h). Cambiar `ADMIN_PASSWORD` cierra todas las sesiones. |
| `api/upload.js` | Sube una foto o PDF a GitHub como *blob* (aún sin publicar). |
| `api/save.js` | Sustituye las zonas editables en `index.html` y crea **un solo commit**. Limpia `<script>`, `on…=` y `javascript:`. |
| `api/history.js` | Lista las últimas versiones y restaura las zonas editables de una de ellas. |
| `api/_lib/` | Código compartido. |

## Puesta en marcha (una sola vez)

En Vercel → proyecto `sport-and-public-policy-website` → **Settings → Environment Variables** (*Production*):

| Variable | Valor |
|---|---|
| `ADMIN_PASSWORD` | La contraseña de Víctor (larga; dársela en persona o por un canal seguro). |
| `GITHUB_TOKEN` | Token *fine-grained* de GitHub: **Repository access** → solo `Sport-and-Public-Policy-website`; **Permissions** → *Contents: Read and write*. Apuntar la fecha de caducidad. |
| `GITHUB_REPO` | `andreasssbh/Sport-and-Public-Policy-website` (valor por defecto). |
| `GITHUB_BRANCH` | `main` (valor por defecto). |

Después, **Deployments → Redeploy** para que las funciones lean las variables. El proyecto de Vercel debe estar
conectado al repositorio (Settings → Git) para que cada «Guardar» se publique solo.

Si el token caduca, «Guardar» dará «GitHub 401»: crear uno nuevo y sustituir `GITHUB_TOKEN`.

## Probar en local

```bash
cp .env.example .env.local   # y poner una ADMIN_PASSWORD de prueba
python3 dev/server.py        # → http://localhost:8787/#acceso
```

`dev/server.py` imita las funciones de `api/` guardando en una copia (`.dev-site/`), sin GitHub ni Vercel.
