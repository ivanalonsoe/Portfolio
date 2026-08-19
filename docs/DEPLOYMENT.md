# Despliegue del portfolio

Este proyecto es un sitio 100% estático (HTML + CSS + JS), generado con el
formato "Claude Design" (`.dc.html`). No hay build ni `npm install`: el propio
`support.js` carga React, ReactDOM y Babel desde un CDN (unpkg) en tiempo de
ejecución. Por eso, para verlo funcionando necesitas:

1. Servir los archivos por HTTP (no abrir el `.html` con doble clic).
2. Tener conexión a internet (para el CDN de React/Babel y los iconos Phosphor).

## Estructura relevante

- `index.html` — punto de entrada (copia de `Portfolio.dc.html`, que es la página real).
- `support.js` — runtime que interpreta las etiquetas `<x-dc>`, `<sc-if>`, `<sc-for>`, etc.
- `image-slot.js` — gestiona los placeholders de imagen.
- `styles.css` — sistema de diseño "Nocturne".
- `_ds/` — documentación/fuente del sistema de diseño (no se sirve en producción, pero no molesta si se publica).
- `Canvas.dc.html` — plantilla vacía, no se usa actualmente.

> ¿Por qué `index.html`? Porque tanto un servidor local como GitHub Pages
> buscan `index.html` en la raíz por defecto. Es una copia literal de
> `Portfolio.dc.html`; si edites el portfolio, edita `Portfolio.dc.html` y
> vuelve a copiarlo a `index.html` (o sustituye `index.html` directamente y
> mantén los dos en sync).

---

## 1. Despliegue en local

Elige **una** de estas opciones (todas sirven el mismo propósito: levantar un
servidor HTTP simple en la carpeta del proyecto).

### Opción A — Python (si ya lo tienes instalado)

```bash
cd e:/Portfolio/Portfolio
python -m http.server 8080
```

Abre en el navegador: http://localhost:8080

### Opción B — Node.js con `serve` (vía pnpm)

Este proyecto no usa `npm`/`npx` por las vulnerabilidades de cadena de
suministro que ha sufrido el ecosistema npm (paquetes comprometidos vía
`postinstall` scripts, typosquatting, etc.). Usamos **pnpm**, que resuelve e
instala de forma más estricta (content-addressable store, sin scripts de
instalación arbitrarios por defecto).

1. Instala pnpm si no lo tienes (una sola vez):
   ```bash
   corepack enable
   corepack prepare pnpm@latest --activate
   ```
   (o `npm install -g pnpm` / `iwr https://get.pnpm.io/install.ps1 -useb | iex` en PowerShell, si prefieres no depender de corepack)

2. Levanta el servidor con el equivalente de `npx` en pnpm:
   ```bash
   cd e:/Portfolio/Portfolio
   pnpm dlx serve .
   ```

Te dará una URL tipo `http://localhost:3000`.

### Opción C — Extensión "Live Server" de VS Code

1. Instala la extensión **Live Server** (ritwickdey.LiveServer).
2. Clic derecho sobre `index.html` → **Open with Live Server**.

### Verificación

- La página debe cargar el header con "Iván Alonso", el hero y las secciones.
- Abre las herramientas de desarrollador (F12) → pestaña **Console** y comprueba
  que no haya errores rojos. Si ves un error cargando `unpkg.com`, revisa tu
  conexión a internet o un firewall/proxy que bloquee ese dominio.
- Prueba el selector de idioma ES/EN y la navegación (Inicio / Currículum / Portfolio).

---

## 2. Despliegue en GitHub Pages

El repo ya tiene un remoto configurado: `https://github.com/ivanalonsoe/Portfolio.git`.

### Paso 1 — Añadir y commitear los archivos nuevos

```bash
git add index.html docs/DEPLOYMENT.md Canvas.dc.html Portfolio.dc.html _ds image-slot.js styles.css support.js .thumbnail
git commit -m "Add portfolio site and deployment docs"
```

(Si prefieres, puedes hacerlo por partes o revisar `git status` antes de cada `add`.)

### Paso 2 — Subir a GitHub

```bash
git push origin main
```

### Paso 3 — Activar GitHub Pages

1. Ve a tu repo en GitHub: `https://github.com/ivanalonsoe/Portfolio`
2. **Settings** → **Pages** (menú lateral izquierdo).
3. En **Build and deployment** → **Source**, selecciona **Deploy from a branch**.
4. En **Branch**, elige `main` y la carpeta `/ (root)`. Guarda.
5. Espera 1-2 minutos. GitHub te mostrará la URL publicada, con este formato:
   `https://ivanalonsoe.github.io/Portfolio/`

### Paso 4 — Verificar

- Abre la URL publicada y repite las comprobaciones del apartado de local
  (consola sin errores, navegación, selector de idioma).
- Cada `git push` a `main` vuelve a desplegar automáticamente (GitHub Pages
  reconstruye en cada push a la rama configurada).

### Notas

- Si más adelante quieres un dominio propio, en la misma pantalla de
  **Settings → Pages** hay un campo **Custom domain**.
- Si prefieres no exponer la carpeta `_ds/` (documentación del design system,
  no necesaria en producción), puedes borrarla del repo o añadirla a un
  `.gitignore` antes del Paso 1. No es obligatorio: no rompe nada si se publica.

---

## 3. Despliegue en Cloudflare Pages (para más adelante)

Cuando quieras dar el salto desde GitHub Pages:

1. Entra en https://dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
2. Autoriza el acceso al repo `ivanalonsoe/Portfolio`.
3. Configuración de build:
   - **Framework preset**: None
   - **Build command**: (vacío)
   - **Build output directory**: `/` (raíz)
4. Deploy. Cloudflare te dará una URL `*.pages.dev` y, opcionalmente, podrás
   añadir un dominio propio.

Como el sitio no requiere build, esta configuración es prácticamente idéntica
a GitHub Pages, solo cambia dónde se aloja.
