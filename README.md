# 🍽️ SIGR · Sistema de Gestión de Restaurante

Aplicación web **full frontend** (HTML5 + CSS3 + JavaScript vanilla, sin frameworks)
con panel de administración para gestionar el menú de un restaurante.

> **Acceso de demostración:** usuario `admin` · contraseña `admin123`

---

## ✨ Características

- **Login exclusivo del administrador** con validación de credenciales.
- **CRUD del menú**: agregar, editar y eliminar platos (nombre, descripción, precio e imagen).
- **Persistencia** en `localStorage` (los datos sobreviven a la recarga).
- **Diseño responsivo** con CSS Grid/Flexbox y variables CSS (tema claro con acentos naranjas).
- **Seguridad básica**: escape de HTML anti-XSS y validación de URL de imagen.
- **Sin dependencias de runtime** — solo se usa Node/Jest para pruebas y Python/Selenium para E2E.

## 📁 Estructura

```
├── index.html              Vista 1 (login) + Vista 2 (panel admin)
├── css/styles.css          Estilos, variables y media queries
├── js/script.js            Lógica ES6+ (secciones 1–10, comentadas)
├── package.json            Scripts npm y configuración de Jest
├── tests/
│   ├── tests_unitarias.js  CAJA BLANCA · Jest (16 pruebas)
│   ├── tests_aceptación.py CAJA NEGRA · Selenium (3 flujos)
│   ├── driver/             ChromeDriver empaquetado (uso offline)
│   └── README.md           Guía completa de la suite de pruebas
└── proyecto_SIGR.zip       Export comprimido del proyecto
```

## 🚀 Puesta en marcha

```bash
# 1. Servidor local (puerto 8080)
python -m http.server 8080 --bind 127.0.0.1
# ó: npm start

# 2. Abrir la app
#    http://127.0.0.1:8080/index.html
```

## 🧪 Pruebas

```bash
npm install            # primera vez (instala Jest)

# Caja Blanca — pruebas unitarias (16)
npm run test:unit
npx jest --verbose     # detalle de cada prueba

# Caja Negra — aceptación con Selenium (3) — con el servidor corriendo
python "tests/tests_aceptación.py"
npm run test:e2e
```

Requisitos: Node 18+, Python 3.10+ con `selenium` (`pip install selenium`),
Google Chrome. El ChromeDriver va incluido en `tests/driver/`.

## 🧰 Tecnologías

| Capa | Tecnología |
|------|------------|
| Estructura | HTML5 semántico |
| Estilos | CSS3 (Grid, Flexbox, variables, media queries) |
| Lógica | JavaScript ES6+ vanilla |
| Pruebas unitarias | Jest + jsdom |
| Pruebas E2E | Python + Selenium WebDriver 4 |

## 📄 Licencia

MIT
