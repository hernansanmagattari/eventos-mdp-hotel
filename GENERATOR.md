# GENERATOR.md — Instrucciones para la actualización semanal automática

Este repositorio publica **un único archivo**: `index.html`, un tablero de inteligencia de
eventos de Mar del Plata para el **Hotel Club del Golf** (Playa Grande). GitHub Pages lo
sirve en la URL del repo.

Una rutina de Claude Code en la nube ejecuta estas instrucciones **una vez por semana**.
Si estás leyendo esto como agente programado: seguí los pasos en orden y al final hacé
commit + push a `main`.

---

## 0. Contexto fijo (no cambia)

- **Hotel:** Club del Golf, Playa Grande, Mar del Plata.
- **Uso:** decisiones de tarifas, paquetes y ocupación del Reservations Manager.
- **Ventana a cubrir:** desde la fecha de ejecución hasta ~6 meses hacia adelante.
- **Categorías válidas:** `Congreso`, `Recital / Show`, `Torneo / Evento Deportivo`,
  `Reunión / Corporativo`, `Feria`.
- **Zonas (cercanía al hotel):** `Playa Grande` > `Zona Sur` > `Centro` > `Otras`.
- **Sedes clave a monitorear siempre:** Hotel Costa Galana, Club Náutico Mar del Plata,
  Espacio Bendu / Bendu Arena, Arena MDQ / Arena Mar del Plata.
- **Seguimiento permanente:** estado del *Fútbol de Verano* del Estadio José María Minella.

---

## 1. Investigación (usar WebSearch / WebFetch)

Hacé, como mínimo, búsquedas para cada uno de estos ejes y quedate solo con lo que tenga
**fuente pública verificable**:

1. `Mar del Plata eventos espectáculos <mes en curso> <año>`
2. `Arena Mar del Plata shows cartelera <año>`
3. `Bendu Arena Mar del Plata shows <temporada/año>`
4. `Mar del Plata congresos convenciones <año>` (revisar también Costa Galana y Sheraton)
5. `Club Náutico Mar del Plata regata / Semana Internacional del Yachting <año>`
6. `Mar del Plata Golf Club torneo abierto <año>`
7. `International Maxi Games Mar del Plata` (si sigue vigente en la ventana)
8. `Estadio José María Minella fútbol de verano <año>` (estado de obras / concesión)
9. `Festival Internacional de Cine de Mar del Plata <año> fechas`

Agendas oficiales a consultar:

- https://turismomardelplata.gob.ar/ASP/SP/eventos-amarlaweb.asp
- https://www.mardelplatabureau.com.ar/calendario-acceso
- https://www.mardelplataturismo.com.ar/recitales.html
- https://cnmp.org.ar/
- https://www.aag.org.ar/
- https://www.internationalmaxigames.com/

### Reglas de calidad de datos (obligatorias)

- **URL fuente real** por evento. Nunca inventar links. Si la fuente es dinámica, usar el
  dominio raíz oficial de la sede o la agenda institucional.
- **Individualizar** la banda / artista / entidad protagonista en el campo `artista`.
- **Sede exacta**; si ocurre en una sede clave, setear `sedeKey` (`costa-galana`,
  `nautico`, `bendu`, `arena-mdq`) — si no, `sedeKey: null`.
- **Estado** honesto: `Confirmado`, `En curso`, `Fecha tentativa`, `En seguimiento`,
  `En duda`. Los eventos anuales sin fecha confirmada van como `Fecha tentativa` con la
  ventana estimada de su edición previa.
- **Impacto:** `Muy Alta`, `Alta`, `Media`, `Baja` (atracción turística + presión sobre la
  ocupación de la plaza).
- **Zona:** cercanía al hotel según la lista de arriba.
- **Notas operativas:** 2-4 frases accionables para el Reservations Manager (tarifa,
  mínimo de noches, tipo de paquete, a quién contactar).
- Priorizar **precisión sobre cantidad**. Es preferible un tablero con 15 eventos sólidos
  que 30 dudosos.
- Marcar `pinned: true` solo en los 2-4 eventos de mayor impacto de todo el período.

---

## 2. Editar `index.html`

**No tocar** el diseño, el CSS ni la lógica JavaScript (funciones `render`, `wire`,
`refreshChrome`, filtros, verificación por `localStorage`, etc.). Modificar **solo**:

1. **`const EVENTOS = [ ... ]`** — reemplazar el arreglo completo por la lista nueva.
   Mantener exactamente la forma de cada objeto:
   ```js
   {
     cat:"Recital / Show",
     nombre:"...",
     artista:"...",
     sede:"...", sedeKey:"arena-mdq",      // o null
     fecha:"...", estado:"Confirmado",
     url:"https://...", urlLabel:"dominio.com",
     impacto:"Alta", zona:"Centro",
     pinned:true,                          // opcional
     notas:"..."
   }
   ```
2. **`const FECHA_BASE = "AAAA-MM-DD";`** — poner la fecha de ejecución de hoy (ISO).
3. En el `<header>`, el texto `Datos compilados: <b>DD/MM/AAAA</b>` — fecha de hoy.
4. En `.disclaimer`, la frase `relevadas el DD/MM/AAAA` — fecha de hoy.
5. El bloque `.callout` del Fútbol de Verano — actualizar el texto si cambió el estado de
   las obras / la concesión / la definición de temporada.
6. Las 4 tarjetas de `.sedes` (Monitoreo de sedes clave) — refrescar el texto de estado de
   cada sede si hubo novedades.

Verificá que el archivo quede como HTML válido y que el `EVENTOS` sea JavaScript válido
(comas, comillas, llaves). Un error de sintaxis deja el tablero en blanco.

---

## 3. Commit + push

```
git add index.html
git commit -m "Actualización semanal del tablero de eventos — AAAA-MM-DD"
git push origin main
```

Mensaje de commit siempre con la fecha ISO del día. No abrir PR: commit directo a `main`.

---

## 4. Si algo falla

- Sin conexión / búsquedas vacías: **no** borres los eventos existentes. Actualizá solo
  las fechas de encabezado y dejá una línea en el commit aclarando que no hubo cambios de
  datos.
- Si `index.html` no existe o está corrupto: regeneralo desde el historial de git
  (`git log`, `git show <commit>:index.html`).
