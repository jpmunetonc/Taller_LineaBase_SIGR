# 🍽️ SIGR · Sistema de Gestión de Restaurante

Aplicación web **full frontend** (HTML5 + CSS3 + JavaScript vanilla, sin frameworks)
con menú público para clientes y panel de administración para gestionar los platos.

> **Acceso de demostración:** usuario `admin` · contraseña `admin123`

---

## 🏗️ Arquitectura · Tres pantallas

```
                    ┌──────────────────────────────────────────┐
   PÚBLICO ───────► │  index.html   ·  Vista del Cliente       │
   (sin login)      │  Menú + selector CUADRÍCULA/LISTA        │
                    │  + CARRITO FLOTANTE + MODAL + PROGRESO   │
                    └───────────────┬──────────────────────────┘
                                    │ enlace "Administrador"
                                    ▼
                    ┌──────────────────────────────────────────┐
   PRIVADO ───────► │  login.html   ·  Autenticación           │
                    │  admin/admin123 → guarda sesión           │
                    └───────────────┬──────────────────────────┘
                                    │ credenciales válidas
                                    ▼
                    ┌──────────────────────────────────────────┐
   PRIVADO ───────► │  admin.html   ·  Panel CRUD              │
   (guard de sesión)│  Alta/edición con IMAGEN LOCAL → Base64   │
                    └──────────────────────────────────────────┘
```

| Pantalla | Archivo | Acceso | Script |
|----------|---------|--------|--------|
| **Índice Cliente** | `index.html` | Público, directo | `js/client.js` |
| **Login Administrador** | `login.html` | Credenciales fijas | `js/login.js` |
| **Panel Admin (CRUD)** | `admin.html` | Requiere sesión | `js/admin.js` |
| *Capa compartida* | — | usada por las 3 | `js/store.js` |

- **`js/store.js`**: datos y lógica pura (validaciones, CRUD, persistencia,
  sesión, imágenes Base64, modo de vista, **carrito de compra** y
  **barra de progreso**). Se expone como `window.SIGR` en el
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
- **Carrito de compras flotante** (esquina inferior derecha):
  - Botón **"🛒 Agregar al pedido"** en cada tarjeta/fila del menú.
  - Icono flotante **permanente** con contador dinámico de elementos.
  - **Modal de resumen**: lista detallada (nombre, precio × cantidad,
    subtotal), **eliminación individual** por plato, **total dinámico**
    y botón **"Confirmar Pedido"**.
  - **Pantalla de estado**: mensaje *"Su pedido está en proceso"* y
    **barra de progreso** que se llena sola del **0 % al 100 % en
    5 segundos** (temporizador JavaScript + transición CSS).
- **Carga de imágenes locales**: `<input type="file" accept="image/*">` +
  `FileReader` → **Base64** (`data:image/…;base64,…`) almacenado con el plato.
- **CRUD completo** del menú (agregar, editar, eliminar) con vista previa,
  validaciones y reversión si se supera la cuota de `localStorage`.
- **Login exclusivo** con validación de credenciales y cierre de sesión.
- **Seguridad básica**: escape de HTML anti-XSS y validación de datos.
- **Sin dependencias de runtime** (Node/Jest solo para pruebas; Python/Selenium para E2E).

## 🛒 Flujo del pedido (Fase 3)

```
  1. Menú (index.html)          2. Icono flotante          3. Modal de resumen
  ┌──────────────────┐          ┌────────────┐          ┌────────────────────────┐
  │ ┌──────────────┐ │  clic    │      🛒    │  clic   │ Resumen de su pedido   │
  │ │ Ceviche  $10 │ │ ───────► │      2     │ ──────► │ · Ceviche  $10.00  🗑️ │
  │ │ [🛒 Agregar] │ │  ×2      │ (fija ab-  │         │ · Lomo     $12.50  🗑️ │
  └──────────────────┘          │   ajo der) │         │ Total           $22.50 │
                                └────────────┘         │ [  Confirmar Pedido  ] │
                                                       └───────────┬────────────┘
                                                                   │ clic
                                                       ┌───────────▼────────────┐
  4. Estado del pedido (5 s)                            │ 👨‍🍳 Su pedido está en   │
  ┌──────────────────────────────────────────────────┐  │     proceso            │
  │ ████████████████████░░░░░░░░░░  60 %             │  │ [████████░░░] 40 %     │
  └──────────────────────────────────────────────────┘  └────────────────────────┘
       → 100 %: "¡Pedido confirmado! Gracias por su compra."
       → el carrito y su contador quedan en 0
```

## 📁 Estructura del proyecto

```
├── index.html              Vista del cliente (público): menú, grid/list,
│                           carrito flotante, modal de resumen y progreso
├── login.html              Autenticación del administrador
├── admin.html              Panel CRUD con imágenes locales
├── css/styles.css          Estilos: variables, media queries, vista de lista,
│                           FAB, modal y barra de progreso (secciones 1–21)
├── js/
│   ├── store.js            Capa de datos y lógica compartida (exportable):
│   │                       validaciones, CRUD, sesión, imágenes, carrito
│   │                       y progreso (0–100 %)
│   ├── client.js           Menú + alternador cuadrícula/lista + carrito,
│   │                       modal de resumen y barra de progreso
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
├── tests_unitarias.js      CAJA BLANCA  · Jest + jsdom  (24 pruebas)
├── tests_aceptación.py     CAJA NEGRA   · Selenium      (6 flujos)
├── driver/chromedriver.exe ChromeDriver empaquetado (uso offline)
└── README.md               Guía detallada de la suite
```

```bash
npm install             # primera vez (instala Jest)

# CAJA BLANCA — 24 pruebas unitarias (12 funcionalidades × 2)
npm run test:unit
npx jest --verbose

# CAJA NEGRA — 6 flujos de aceptación (con el servidor corriendo)
python "tests/tests_aceptación.py"
npm run test:e2e
```

**Caja Blanca (24):** credenciales · validación de platos · alta · edición ·
eliminación · persistencia · formato de precio · escape XSS ·
imagen local → Base64 · alternancia cuadrícula/lista ·
**carrito: sumar/restar/total** · **barra de progreso 0–100 %**.

**Caja Negra (6):** login exitoso · login fallido · cierre de sesión ·
cliente alterna cuadrícula/lista · alta con imagen local publicada al cliente ·
**pedido completo: agregar → modal → confirmar → barra de progreso**.

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
