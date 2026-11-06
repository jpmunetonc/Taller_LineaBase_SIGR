# 🍽️ SIGR · Sistema de Gestión de Restaurante

Aplicación web **full frontend** (HTML5 + CSS3 + JavaScript vanilla, sin frameworks)
con menú público para clientes y panel de administración para gestionar los platos.

> **Acceso de demostración:** usuario `admin` · contraseña `admin123`

---

## 🏗️ Arquitectura · Tres pantallas

```
                    ┌─────────────────────────────────────────┐
   PÚBLICO ───────► │  index.html   ·  Vista del Cliente      │
   (sin login)      │  Menú dinámico + selector CUADRÍCULA/LISTA │
                    └───────────────┬─────────────────────────┘
                                    │ enlace "Administrador"
                                    ▼
                    ┌─────────────────────────────────────────┐
   PRIVADO ───────► │  login.html   ·  Autenticación          │
                    │  admin/admin123 → guarda sesión          │
                    └───────────────┬─────────────────────────┘
                                    │ credenciales válidas
                                    ▼
                    ┌─────────────────────────────────────────┐
   PRIVADO ───────► │  admin.html   ·  Panel CRUD             │
   (guard de sesión)│  Alta/edición con IMAGEN LOCAL → Base64  │
                    └─────────────────────────────────────────┘
```

| Pantalla | Archivo | Acceso | Script |
|----------|---------|--------|--------|
| **Índice Cliente** | `index.html` | Público, directo | `js/client.js` |
| **Login Administrador** | `login.html` | Credenciales fijas | `js/login.js` |
| **Panel Admin (CRUD)** | `admin.html` | Requiere sesión | `js/admin.js` |
| *Capa compartida* | — | usada por las 3 | `js/store.js` |

- **`js/store.js`**: datos y lógica pura (validaciones, CRUD, persistencia,
  sesión, imágenes Base64 y modo de vista). Se expone como `window.SIGR` en el
  navegador y como módulo CommonJS en Node/Jest.
- **Protección de sesión**: `admin.html` redirige a `login.html` si no hay
  sesión activa; `login.html` salta al panel si ya hay sesión.
- **`localStorage`** comparte el origen entre las tres páginas, por lo que el
  menú publicado por el admin aparece de inmediato en el índice del cliente.

## ✨ Características

- **Menú público** con render dinámico desde `localStorage`.
- **Selector de vista** (esquina superior derecha):
  - 🔲 **Cuadrícula** → tarjetas organizadas por columnas (CSS Grid).
  - 📋 **Lista** → filas horizontales, un plato debajo del otro (Flexbox).
  - La preferencia se **persiste** y la transición es animada.
- **Carga de imágenes locales**: `<input type="file" accept="image/*">` +
  `FileReader` → **Base64** (`data:image/…;base64,…`) almacenado con el plato.
- **CRUD completo** del menú (agregar, editar, eliminar) con vista previa,
  validaciones y reversión si se supera la cuota de `localStorage`.
- **Login exclusivo** con validación de credenciales y cierre de sesión.
- **Seguridad básica**: escape de HTML anti-XSS y validación de datos.
- **Sin dependencias de runtime** (Node/Jest solo para pruebas; Python/Selenium para E2E).

## 📁 Estructura del proyecto

```
├── index.html              Vista del cliente (público, grid/list)
├── login.html              Autenticación del administrador
├── admin.html              Panel CRUD con imágenes locales
├── css/styles.css          Estilos, variables, media queries y vista de lista
├── js/
│   ├── store.js            Capa de datos y lógica compartida (exportable)
│   ├── client.js           Render del menú + alternador cuadrícula/lista
│   ├── login.js            Validación de credenciales y sesión
│   └── admin.js            CRUD, FileReader→Base64 y guard de sesión
├── package.json            Scripts npm y configuración de Jest
├── README.md               Este archivo
└── tests/                  Ver "Suite de pruebas" más abajo
```

## 🚀 Puesta en marcha

```bash
# 1. Servidor local (puerto 8080)
python -m http.server 8080 --bind 127.0.0.1
# ó: npm start

# 2. Abrir la app
#    http://127.0.0.1:8080/index.html     ← cliente (público)
#    http://127.0.0.1:8080/login.html     ← administrador
```

## 🧰 Claves de localStorage

| Clave | Contenido |
|-------|-----------|
| `sigr_menu_platos` | Arreglo JSON de platos (imagen en Base64 o URL) |
| `sigr_session` | Sesión activa del administrador |
| `sigr_view_mode` | Preferencia de vista: `grid` o `list` |

## 🧪 Suite de pruebas (`tests/`)

```
tests/
├── tests_unitarias.js      CAJA BLANCA  · Jest + jsdom  (20 pruebas)
├── tests_aceptación.py     CAJA NEGRA   · Selenium      (5 flujos)
├── driver/chromedriver.exe ChromeDriver empaquetado (uso offline)
└── README.md               Guía detallada de la suite
```

```bash
npm install             # primera vez (instala Jest)

# CAJA BLANCA — 20 pruebas unitarias (10 funcionalidades × 2)
npm run test:unit
npx jest --verbose

# CAJA NEGRA — 5 flujos de aceptación (con el servidor corriendo)
python "tests/tests_aceptación.py"
npm run test:e2e
```

**Caja Blanca (20):** credenciales · validación de platos · alta · edición ·
eliminación · persistencia · formato de precio · escape XSS ·
**imagen local → Base64** · **alternancia cuadrícula/lista**.

**Caja Negra (5):** login exitoso · login fallido · cierre de sesión ·
**cliente alterna cuadrícula/lista** · **alta con imagen local publicada al cliente**.

Requisitos: Node 18+, Python 3.10+ con `selenium` (`pip install selenium`) y
Google Chrome. Detalles y variables de entorno en [`tests/README.md`](tests/README.md).

## 🛠️ Tecnologías

| Capa | Tecnología |
|------|------------|
| Estructura | HTML5 semántico (3 páginas) |
| Estilos | CSS3 (Grid, Flexbox, variables, animaciones, media queries) |
| Lógica | JavaScript ES6+ vanilla modular (4 scripts) |
| Imágenes | `FileReader` + Data URLs Base64 |
| Pruebas unitarias | Jest + jsdom |
| Pruebas E2E | Python + Selenium WebDriver 4 |

## 📄 Licencia

MIT
