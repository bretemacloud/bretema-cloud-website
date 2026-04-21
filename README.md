# Brétema Cloud · Sitio web

Web estática multidioma (gallego / castellano) servida por GitHub Pages.

## Estructura

```
.
├── index.html          ← Página raíz: detecta idioma del navegador y redirige
├── CNAME               ← Dominio personalizado (bretemacloud.com)
├── gl/
│   └── index.html      ← Versión gallega (idioma por defecto)
└── es/
    └── index.html      ← Versión castellana
```

URLs resultantes una vez desplegado:

- `bretemacloud.com/` → redirige según idioma del navegador
- `bretemacloud.com/gl/` → versión gallega
- `bretemacloud.com/es/` → versión castellana

## Despliegue inicial (15 minutos)

### 1. Crear repositorio en GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/TU_USUARIO/bretema-web.git
git branch -M main
git push -u origin main
```

El repo puede ser público (gratis siempre) o privado (gratis con cuenta GitHub Pro/Team).

### 2. Activar GitHub Pages

En el repositorio: **Settings → Pages**

- **Source:** Deploy from a branch
- **Branch:** `main` / `/ (root)`
- Pulsar **Save**

GitHub te asignará una URL temporal tipo `https://TU_USUARIO.github.io/bretema-web/`. Confirma que carga antes de seguir.

### 3. Configurar el dominio personalizado

En la misma pantalla de **Settings → Pages**:

- **Custom domain:** `bretemacloud.com`
- Pulsar **Save**

Esto crea (o sobrescribe) el archivo `CNAME` en el repo. Como ya existe en este repo, se respetará.

### 4. Configurar el DNS

En tu proveedor de DNS (donde tengas registrado `bretemacloud.com`), añade los siguientes registros:

**A records (apex → bretemacloud.com):**

```
Type: A    Name: @    Value: 185.199.108.153    TTL: 3600
Type: A    Name: @    Value: 185.199.109.153    TTL: 3600
Type: A    Name: @    Value: 185.199.110.153    TTL: 3600
Type: A    Name: @    Value: 185.199.111.153    TTL: 3600
```

**AAAA records (IPv6, opcional pero recomendado):**

```
Type: AAAA    Name: @    Value: 2606:50c0:8000::153    TTL: 3600
Type: AAAA    Name: @    Value: 2606:50c0:8001::153    TTL: 3600
Type: AAAA    Name: @    Value: 2606:50c0:8002::153    TTL: 3600
Type: AAAA    Name: @    Value: 2606:50c0:8003::153    TTL: 3600
```

**CNAME (www → apex):**

```
Type: CNAME    Name: www    Value: TU_USUARIO.github.io.    TTL: 3600
```

(Reemplaza `TU_USUARIO` con tu usuario de GitHub.)

### 5. Esperar propagación DNS y activar HTTPS

La propagación tarda entre 5 minutos y 24 horas. Puedes comprobarla con:

```bash
dig bretemacloud.com +short
# Debe devolver las 4 IPs de GitHub Pages
```

Cuando GitHub detecte que el DNS apunta correctamente, en **Settings → Pages** aparecerá la opción **Enforce HTTPS**. Márcala. El certificado Let's Encrypt se renueva automáticamente.

## Despliegue de cambios

A partir de ahora, cualquier cambio se publica con un push:

```bash
git add .
git commit -m "Actualizar copy de la sección de IA"
git push
```

GitHub Pages reconstruye y publica en menos de un minuto.

## Rollback

Si algo se rompe en producción:

```bash
# Ver historial
git log --oneline

# Revertir el último commit
git revert HEAD
git push

# O volver a un commit concreto
git revert <hash-del-commit-malo>
git push
```

## Coste

**0 €/mes** mientras GitHub Pages siga siendo gratuito (lo es desde 2008 sin signos de cambio). El único coste es la renovación anual del dominio `bretemacloud.com`.

## Cuándo migrar a S3 + CloudFront

Esta configuración aguanta perfectamente los primeros años. Considera migrar cuando:

- Necesites redirección por idioma en el *edge* (sin parpadeo de JS)
- Quieras logs detallados o A/B testing
- Vayas a integrar lógica server-side (Lambda@Edge)
- El equipo crezca y queráis CI/CD avanzado con previews por PR
- Necesites cumplimiento estricto que exija control total de la infraestructura

Hasta entonces: GitHub Pages.
