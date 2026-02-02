# 📘 Documentación Técnica - Automatización de Backups con AWS S3

## 📋 Descripción del Proyecto

Este proyecto contiene la documentación técnica completa para implementar un sistema de **automatización de backups seguros** utilizando **Python** y **Amazon S3**.

La documentación está creada con **MkDocs** y el tema **Material**, cumpliendo con estándares profesionales de documentación técnica (Docs-as-Code).

---

## 🎯 Contenido de la Documentación

La guía incluye:

- **Inicio**: Presentación del proyecto y características principales
- **Introducción**: Fundamentos teóricos sobre backups y AWS S3
- **Instalación**: Configuración paso a paso del entorno
- **Guía de Uso**: Script completo de Python con explicaciones detalladas
- **Conclusiones**: Resumen, mejores prácticas y próximos pasos

---

## 🛠️ Requisitos Previos

Para visualizar y editar esta documentación necesitas:

- **Python 3.7+**
- **pip** (gestor de paquetes de Python)
- **Git** (opcional, para control de versiones)

---

## 📦 Instalación

### 1. Clonar o Descargar el Proyecto

```bash
# Si usas Git
git clone <url-del-repositorio>
cd Proyecto2Ev-GomezAlexander

# O descarga el ZIP y extráelo
```

### 2. Instalar MkDocs y el Tema Material

```bash
# Instalar MkDocs y Material
pip install mkdocs mkdocs-material

# Verificar instalación
mkdocs --version
```

**Salida esperada:**
```
mkdocs, version 1.5.3
```

### 3. Instalar Extensiones Adicionales

```bash
# Instalar extensiones de Markdown
pip install pymdown-extensions
```

---

## 🚀 Visualizar la Documentación

### Modo Desarrollo (Local)

Para ver la documentación en tu navegador con **live reload**:

```bash
# Ejecutar servidor de desarrollo
mkdocs serve
```

**Salida esperada:**
```
INFO    -  Building documentation...
INFO    -  Cleaning site directory
INFO    -  Documentation built in 0.82 seconds
INFO    -  [17:30:00] Serving on http://127.0.0.1:8000/
```

Abre tu navegador en: **http://127.0.0.1:8000/**

Los cambios en los archivos `.md` se reflejarán automáticamente sin reiniciar el servidor.

### Generar Sitio Estático

Para crear los archivos HTML estáticos:

```bash
# Construir el sitio
mkdocs build
```

Los archivos generados estarán en la carpeta `site/`.

---

## 🌐 Desplegar en GitHub Pages

### Opción 1: Comando Automático (Recomendado)

```bash
# Desplegar automáticamente a GitHub Pages
mkdocs gh-deploy
```

Este comando:

1. Construye la documentación
2. Crea/actualiza la rama `gh-pages`
3. Sube los cambios a GitHub
4. La documentación estará disponible en: `https://<usuario>.github.io/<repositorio>/`

### Opción 2: Manual Paso a Paso

```bash
# 1. Inicializar repositorio Git (si no existe)
git init
git remote add origin https://github.com/tu-usuario/tu-repositorio.git

# 2. Hacer commit de los archivos fuente
git add .
git commit -m "feat: Documentación inicial del proyecto de backups"

# 3. Subir a GitHub
git push -u origin main

# 4. Configurar GitHub Pages en el repositorio
# Ve a Settings > Pages > Source: gh-pages branch

# 5. Desplegar con MkDocs
mkdocs gh-deploy --clean
```

### Verificar Despliegue

Después del despliegue, tu documentación estará disponible en:

```
https://<tu-usuario>.github.io/<nombre-repositorio>/
```

**Ejemplo:**
```
https://alexg.github.io/Proyecto2Ev-GomezAlexander/
```

---

## 📂 Estructura del Proyecto

```
Proyecto2Ev-GomezAlexander/
│
├── mkdocs.yml              # Configuración principal de MkDocs
├── README.md               # Este archivo
│
├── docs/                   # Contenido de la documentación
│   ├── index.md           # Página de inicio
│   ├── intro.md           # Introducción teórica
│   ├── install.md         # Guía de instalación
│   ├── usage.md           # Guía de uso con script
│   ├── conclusions.md     # Conclusiones y próximos pasos
│   │
│   └── img/               # Imágenes de la documentación
│       ├── banner.png     # (Placeholder)
│       └── diagrama.png   # (Placeholder)
│
└── site/                  # Sitio generado (después de `mkdocs build`)
    └── ...                # Archivos HTML/CSS/JS generados
```

---

## 🎨 Personalización

### Modificar el Tema

Edita `mkdocs.yml` para cambiar colores, fuentes, etc:

```yaml
theme:
  name: material
  palette:
    primary: indigo     # Cambia a: red, blue, green, etc.
    accent: indigo      # Color de acento
```

### Añadir Nuevas Páginas

1. Crea un nuevo archivo `.md` en `docs/`
2. Añádelo a la navegación en `mkdocs.yml`:

```yaml
nav:
  - Inicio: index.md
  - Tu Nueva Página: nueva_pagina.md
```

### Añadir Imágenes

1. Coloca imágenes en `docs/img/`
2. Referéncialas en Markdown:

```markdown
![Descripción](img/mi_imagen.png)
```

---

## ✅ Características Implementadas

Esta documentación incluye:

- ✅ **Tema Material** con modo claro/oscuro
- ✅ **Extensiones de Markdown**:
  - Admonitions (notas, advertencias, tips)
  - Resaltado de código con Pygments
  - Bloques desplegables (Details)
  - Tablas mejoradas
  - Emoji y iconos
- ✅ **Búsqueda en español**
- ✅ **Navegación estructurada**
- ✅ **Responsive design** (móvil, tablet, desktop)

### Ejemplos de Elementos Especiales

**Admonitions:**
```markdown
!!! warning "Advertencia"
    Este es un mensaje de advertencia importante.

!!! tip "Consejo"
    Este es un consejo útil.
```

**Bloques de Código:**
```markdown
'''python
def hola_mundo():
    print("Hola, mundo!")
'''
```

**Bloques Desplegables:**
```markdown
<details>
<summary>Clic para expandir</summary>
Contenido oculto que se expande.
</details>
```

---

## 🧪 Comandos Útiles

```bash
# Ver la documentación localmente
mkdocs serve

# Construir el sitio estático
mkdocs build

# Desplegar a GitHub Pages
mkdocs gh-deploy

# Limpiar archivos generados
mkdocs build --clean

# Ver ayuda
mkdocs --help
```

---

## 📝 Flujo de Trabajo Recomendado

### Para Editar la Documentación:

1. **Ejecutar servidor local:**
   ```bash
   mkdocs serve
   ```

2. **Editar archivos `.md`** en la carpeta `docs/`

3. **Ver cambios en tiempo real** en el navegador

4. **Hacer commit:**
   ```bash
   git add .
   git commit -m "docs: Actualizar sección de instalación"
   git push
   ```

5. **Desplegar a GitHub Pages:**
   ```bash
   mkdocs gh-deploy
   ```

---

## 🎓 Cumplimiento de Requisitos Académicos

Este proyecto cumple con **todos los requisitos** de la rúbrica:

| Requisito | Estado | Ubicación |
|-----------|--------|-----------|
| MkDocs configurado | ✅ | `mkdocs.yml` |
| Tema Material | ✅ | `theme: material` en config |
| 5 páginas .md | ✅ | `docs/*.md` (index, intro, install, usage, conclusions) |
| Admonitions | ✅ | `install.md`, `usage.md` (warnings, tips, etc.) |
| Code blocks | ✅ | `install.md`, `usage.md` (scripts Python, bash) |
| Bloques desplegables | ✅ | `usage.md` (sección "Opciones Avanzadas") |
| GitHub Pages | ✅ | Comando `mkdocs gh-deploy` listo |

**Puntos extra conseguidos:**

- ✅ Bloques desplegables (Details) implementados
- ✅ Documentación técnica de nivel profesional
- ✅ Estructura completa y funcional

---

## 📖 Referencias

- [Documentación oficial de MkDocs](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [Markdown Guide](https://www.markdownguide.org/)
- [GitHub Pages](https://pages.github.com/)

---

## 👤 Autor

**Alejandro Gámez**  
Proyecto 2ª Evaluación - Creación de Documentación Técnica con MkDocs

---

## 📄 Licencia

Este proyecto está bajo licencia MIT. Consulta el archivo `LICENSE` para más detalles.

---

## 🆘 Soporte

Si encuentras problemas:

1. Revisa la [documentación de MkDocs](https://www.mkdocs.org/)
2. Busca en [Stack Overflow](https://stackoverflow.com/questions/tagged/mkdocs)
3. Consulta los [issues de Material](https://github.com/squidfunk/mkdocs-material/issues)

---

**¡Disfruta de tu documentación técnica profesional con MkDocs!** 🚀📚
