# Mapeo de Vía · EFE Sur

App web (un solo archivo) para **medir la Línea 1 y la Línea 2 con el GPS del
teléfono** y dejar marcadas estaciones, cruces y cambios de vía. Sirve para
reemplazar el trazado aproximado (muestreado cada 500 m) que usan las
prevenciones de la bitácora por uno medido en terreno.

## Qué hace

- Graba **1 punto cada segundo, sin excepción y sin filtros de metros** (lat, lon, precisión, velocidad, rumbo, altura, distancia). Si en ese segundo el GPS no alcanzó a entregar posición nueva, igual se guarda el punto con `nuevo = 0` y la edad del dato.
- Botones grandes para la **vía**: V1, V2, V4, V8. Cada cambio queda marcado en el punto exacto.
- **Sentido**: Concepción → Coronel / Coronel → Concepción (L2) y Laja ↔ Mercado (L1).
- **Estaciones**: eliges y tocas *Marcar estación*; salta sola a la siguiente del sentido.
- **Cruces y puntos**: cruce vehicular, peatonal, paso nivel, puente, túnel, desvío, señal u otro, con nombre opcional.
- **Prevenciones**: botón **INICIO** al entrar y **FIN** al salir, con el PK del boletín de cada extremo (ej. PK 6 P1 → PK 6 P30) y la restricción. Los PK se escriben antes, durante o después. La tabla compara el largo del boletín con el medido.
- **Postes sueltos**: marca un poste cualquiera y anota su PK.
- **PK calibrado**: cada poste con PK del boletín (postes sueltos e inicio/fin de prevenciones) corrige el PK de todo el recorrido: entre dos postes se interpola por distancia recorrida y fuera de ellos se extrapola desde el más cercano. Un PK que no calza con el sentido de marcha se marca en rojo y no se usa. Convención del boletín: PK 6 P30 = 6,30.
- **Distancia, dos medidas**: `d` integra la velocidad que mide el GPS (no suma el temblor de posición con el tren detenido) y `dg` suma las posiciones tal cual, sin filtro.
- **PK medido**: se ancla en Concepción (L2) o San Rosendo (L1) = PK 0 si está marcada; si no, en la primera estación marcada usando el PK de la bitácora. La tabla *Medición por tramo* compara PK medido vs. PK de la bitácora.
- Todo se guarda en el teléfono (IndexedDB) y se recupera si se cierra la página.
- Funciona sin señal una vez abierta (service worker).

## Exportar

En *Recorridos guardados*:

| Archivo | Contenido |
|---|---|
| **JSON completo** | Todos los puntos segundo a segundo con su PK calibrado, más los tramos con estaciones, cruces, cambios de vía, prevenciones (inicio/fin, largo boletín vs. medido) y postes, cada uno con PK del boletín, PK medido y PK sin calibrar |
| **CSV** | Un punto por fila, abre en Excel |
| **GeoJSON** | Para verlo en Google My Maps, geojson.io o QGIS |

En Android se abre el menú *Compartir* (WhatsApp, Drive, correo).

## Uso en el tren

1. Abrir la página con buena señal y aceptar el permiso de ubicación.
2. Elegir línea, sentido y vía → **INICIAR**.
3. **No bloquear la pantalla** ni cambiar de app: el navegador deja de entregar GPS en segundo plano. La app pide mantener la pantalla encendida.
4. Marcar la estación de salida apenas empieza (idealmente Concepción, que es PK 0 en L2).
5. Al final, **TERMINAR** y exportar el JSON.

Precisión típica del GPS de un teléfono: 3 a 10 m a cielo abierto; peor en
trincheras, bajo estructuras y dentro de la cabina. Conviene hacer varias
pasadas (ida y vuelta) y promediar.
