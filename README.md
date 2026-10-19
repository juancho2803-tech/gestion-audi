# Gestión Operativa Audi

Tablero BI para unificar el seguimiento de órdenes de taller Audi (chapa/pintura,
garantía, interna, externa) con carga diaria de la base y persistencia
compartida vía Google Sheets + Apps Script.

## Estructura del repo

```
index.html      → el tablero (frontend, se hostea con GitHub Pages)
gas/Code.gs      → backend Apps Script (se pega en el editor de Google Sheets)
```

## 1. Backend (Apps Script + Google Sheets)

1. Creá una Google Sheet nueva (o usá una existente) — va a ser la base de datos.
2. Extensiones → Apps Script.
3. Borrá el contenido de `Code.gs` que viene por defecto y pegá el contenido
   de `gas/Code.gs` de este repo.
4. Guardá el proyecto.
5. Implementar → Nueva implementación → tipo **Aplicación web**.
   - Ejecutar como: **Yo**
   - Quién tiene acceso: **Cualquier usuario**
6. Autorizá los permisos que pida Google (es tu propia cuenta/Sheet).
7. Copiá la **URL de la aplicación web** que te da al finalizar
   (termina en `/exec`).

### Actualizar el script sin cambiar la URL

Cuando cambie `Code.gs`: pegá el código nuevo, guardá, y después
**Implementar → Administrar implementaciones → lápiz (editar) → Versión: "Nueva versión" → Implementar.**

No uses "Nueva implementación": crea una URL distinta y el tablero sigue
hablando con la versión vieja.

### Seguridad de la base

- La carga nueva se escribe primero en una hoja temporal; recién si termina bien
  reemplaza a `Datos`. Si falla, `Datos` queda intacta.
- La carga anterior queda en la hoja `Datos_backup`.
- Antes de subir, el tablero verifica que el script publicado sea la versión
  actual; si es vieja, no sube nada y avisa.

## 2. Conectar el frontend

1. Abrí `index.html` (local o ya publicado en GitHub Pages).
2. Click en el ícono de engranaje (⚙) junto al selector de tema.
3. Pegá la URL `.../exec` que copiaste del paso anterior → **Guardar**.
4. A partir de ahí, cada carga de base (Excel .xlsx; el CSV no se admite) se guarda automáticamente
   en la Google Sheet, y al abrir el tablero desde cualquier compu se trae
   la última base cargada.

Sin esa URL configurada, el tablero sigue funcionando igual pero guarda los
datos solo en el navegador (localStorage) — útil para probar antes de
conectar el backend.

## 3. Publicar en GitHub Pages

```
git init
git add .
git commit -m "Bloque 1: tablero resumen + backend Apps Script"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/gestion-audi.git
git push -u origin main
```

Repo → Settings → Pages → Source: "Deploy from a branch" → `main` / `/ (root)`.
Queda accesible en `https://TU-USUARIO.github.io/gestion-audi/`.

## Sección Gerencia

Ingresos de vehículos (día / semana / mes, por tipo: auto, SUV, pick-up, utilitario), comparación con el
mes anterior, proyección de cierre del mes y de los próximos 3 meses, motivos de visita, services por
kilometraje e indicadores de servicio (turnos, estadía, recurrencia, km al ingreso).

Incluye además "El negocio mes a mes" (12 meses con variación contra el mes anterior), modelos que vienen
más o dejan de venir, y clientes y fidelización (clientes y vehículos más fieles, retención de 0 km y lista
de services a vencer descargable en Excel).

Usa columnas que se agregaron en las versiones 3 y 4 del backend: Modelo, kilometraje, Chasis, Dias en
taller, Tarea 1 a 9 y DNI. Después de actualizar `Code.gs` hay que publicar una nueva versión del Apps Script y volver
a cargar el Excel para que esos datos queden guardados en la hoja.

Para que la evolución y la proyección sean reales, el Excel tiene que traer todas las órdenes abiertas en
los últimos 12 meses calendario. Los meses que vienen incompletos se detectan, se marcan con * y no se usan para proyectar.

## Diferencias con la versión VW

- Claves de localStorage propias (`tablero_audi_*`), así no se pisa con el tablero VW (comparten dominio github.io).
- Mapeo Asesor → Sucursal en `SUC_MAP` (index.html), a completar con los asesores de Audi.
- Familias de modelos Audi (A1…A8, Q2…Q8, TT, R8, e-tron) para la sección Gerencia.
- Requiere su propia Google Sheet + Apps Script (no reutilizar la URL /exec de VW).
