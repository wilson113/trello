# Tablero

Gestor de tareas tipo Trello (tableros kanban) en un solo archivo HTML, sin dependencias ni servidor.

- Varios tableros, columnas y tarjetas arrastrables (ratón y táctil)
- Etiquetas, listas de tareas, portadas, fondos, tarjetas fijadas y columnas bloqueadas
- Los datos se guardan en el navegador (`localStorage` + IndexedDB para las imágenes)
- Exportar/Importar copias en JSON

## Uso en local
Abre `index.html` en el navegador.

## Publicación
Se sirve con GitHub Pages desde la rama `main`. Los datos son privados de cada navegador:
no se sincronizan entre dispositivos; usa Exportar e Importar para moverlos.

Notas para quien mantenga el código en [AGENTS.md](AGENTS.md).
