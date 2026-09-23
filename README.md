# Cash Flow Proyectado · Tesorería

App estática para el seguimiento de cash flow de TF Carnes y Trade Food. Se publica con GitHub Pages.

## Estructura

- `index.html` — markup
- `css/style.css` — estilos
- `js/app.js` — lógica de la app (parseo de Excel, cálculo del cash flow, sincronización con Supabase, render)
- `js/app-patches.js` — funcionalidades agregadas después, por monkey-patch sobre `app.js` (se carga
  después y reasigna `renderModoB`/`renderAll` para no tener que tocar `app.js` en cada punto donde
  ya se llaman): cotización USD→ARS de Modo B (`setModoBCotizacion`, tarjeta "Total equiv. pesos" y
  "Total compromisos") y la sección "Resumen de Posición" (`renderResumen`, TF Carnes vs. Trade Food
  lado a lado, usando los mismos datos que ya calculan las tarjetas KPI de cada empresa).

## Datos

- Los balances de tesorería y cheques físicos se leen en vivo desde Google Sheets (deben estar compartidos como "cualquiera con el enlace puede ver").
- Las subidas manuales de Excel (Cuentas a pagar, Cheques cartera/banco, Modo B, Compromisos de efectivo) se guardan en un proyecto de Supabase propio, para que persistan entre navegadores y no solo en `localStorage`.
- La sección "Compromisos de Efectivo · Financiera" (solo TF Carnes) solo muestra la vista CFO
  (fecha de entrega y monto por cliente). El desglose de cheques a emitir a la financiera (bruto/neto/
  costo 3%, `calcPlanFinanciera()`) ya no se muestra en pantalla, pero la función se mantiene y se sigue
  usando en la hoja "Compromisos Efectivo" del Excel exportado (⬇ Excel).

## Supabase

Proyecto: `cashflow-tesoreria` (región `sa-east-1`).

Tablas (`public`): `cheques_cartera`, `cuentas_a_pagar`, `modo_b_compromisos`, `compromisos_efectivo`. Cada subida de Excel reemplaza por completo los datos previos de esa empresa/tabla.

La app usa la `anon` key (visible en el propio HTML, es pública por diseño) y las tablas tienen RLS habilitado con policies abiertas para `anon`, ya que la app no tiene sistema de login propio.

## Deploy

GitHub Pages sirve directamente `index.html` desde la rama `main`.

`css/style.css` y `js/app.js` se referencian con un query string de versión
(`?v=AAAAMMDDx`) para evitar que el navegador quede con una copia vieja en caché.
Al hacer un cambio en CSS o JS, subir también ese número en `index.html`.
