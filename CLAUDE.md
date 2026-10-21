# CLAUDE.md — Tienda LIVES (Tienda-lives)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
- `index.html` — **caja de la tienda**:
  - Vender: tipo de cliente (Público, Empleado, Obrero), "Precio exclusivo" y "Descuento por planilla" (no Público). Datos del cliente opcionales (con DNI de 8 dígitos se autocompleta). La prenda se arma en 5 pasos: marca → tipo de prenda (por género) → estilo → color → talla; el precio sale del tipo de prenda y solo se rebaja si no es Público o es exclusivo, nunca por encima de la lista. Cada paso permite agregar, modificar o quitar valores del catálogo. El código LT-XXXXXX lo asigna la base al cobrar. Formas de pago: Efectivo, Yape / Plin, Tarjeta, Transferencia (o descuento por planilla).
  - "Ventas de hoy" con + Cliente, Corregir y Anular por ticket. Registro de ventas con filtros, KPIs, regla 500, corrección de boleta completa y Excel LIVES (sin datos personales).
  - La cuenta del portal es compartida: quien vende se elige con botones y se guarda en el equipo; sin vendedora no se cobra, corrige ni anula.
  - Un token por intento evita duplicar una venta. No hay control de stock ni cola sin conexión.
- `ventas.html` — **dashboard de gerencia**, solo consulta: año/mes o fechas, marca y agrupación (día a año); KPIs (ingresos con variación, prendas, precio promedio, ticket promedio, venta por día) y pestañas Tendencia, Año contra año, Clientes y tickets, Quién compra, Qué se vende y Detalle por prenda. Desde el 19/06/2026 hay registro prenda por prenda; antes, resumen histórico. Excel de clientes que más compran e Imprimir / PDF.

## Base de datos (Supabase lives-datos)
- Una sola puerta: `tienda_api(p)` con `p.action`.
  - Leer: `catalogos`, `ventas`, `historico`, `buscar_cliente`, `skus`.
  - Registrar: `venta`, `anular`, `editar_ticket`, `cliente`, `guardar_contacto`, `agregar`, `editar`, `eliminar` (si ya tiene ventas, se desactiva).
- Tablas: `tienda_ventas`, `tienda_skus`, catálogos (`tienda_categorias`, `tienda_colores`, `tienda_marcas`, `tienda_estilos`, `tienda_tallas`), `tienda_vendedoras` y el histórico (`tienda_hist_dia`, `tienda_hist_cat`, `tienda_hist_clientes`).
- Datos personales de clientes (DNI, celular, correo): solo los ve quien tiene permiso de la tienda; sin usuario no se leen (21/10/2026).
- No usa Storage.

## Quién la usa
Según las entradas por el portal de los últimos 30 días al 21/10/2026: la cuenta de venta en tienda y Datos e IA, más gerencia en el dashboard.

## Quién puede vender
- Lo decide la base: `tienda_permiso()` devuelve 'venta' o 'consulta' según la tabla `permisos`; solo 'venta' registra. Sin usuario, solo consulta y sin datos de clientes.
- A Sistemas: `tienda_permiso` es la pieza que reemplazan con su seguridad.

## Pendientes vistos al escribir esto (21/10/2026)
- `index.html`: en `agregar()` el aviso "Agregado: …" usa una variable no definida (`c`); la prenda sí entra al carrito, pero el aviso no sale.
- `index.html` trae las vistas Configuración (libreta de clientes, códigos creados) y Dashboard, sin botón para llegar a ellas.
- `ventas.html`: la vista Registro no tiene acceso y su `ir()` tiene un `if(v==='cfg')` sin cuerpo que impediría cargarla; el aviso "Todavía no hay datos de clientes" nunca aparece (condición mal anidada).
