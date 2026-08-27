# Tablero de Eventos — Mar del Plata · Hotel Club del Golf

Tablero de inteligencia comercial que compila y jerarquiza los eventos de los próximos
meses en Mar del Plata (congresos, recitales, torneos, ferias y reuniones corporativas)
para las decisiones de tarifas, paquetes y ocupación del Hotel Club del Golf.

- **Ver el tablero:** una vez habilitado GitHub Pages, en
  `https://<usuario>.github.io/<repo>/`
- **Actualización:** una rutina de Claude Code en la nube regenera `index.html` cada
  semana siguiendo [`GENERATOR.md`](GENERATOR.md).
- **Fuente de datos:** prensa local, sitios oficiales de sedes, ticketeras y agendas
  institucionales. Cada tarjeta enlaza su fuente para auditoría humana.

El archivo `index.html` es autocontenido (HTML + CSS + JS, sin dependencias externas) y se
puede abrir también de forma local en cualquier navegador.
