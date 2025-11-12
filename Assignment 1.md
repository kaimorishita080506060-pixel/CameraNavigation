# Worksheet - Hosting an Interactive Map with Node.js

---

### Guide

Goal: start a Node.js local server and host a sample interactive HTML, then view it in a browser.

**Folder setup**

```
node-map-lab/
  index.html
```

**Steps**

1. Install Node.js (LTS). Once installed, verify in terminal:

```bash
node -v
npm -v
```

2. Create the project folder and sample page.
   Terminal:

```bash
mkdir node-map-lab && cd node-map-lab
```

Create `index.html` in your editor with this content:

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Node Local Server</title>
  <style>body{font:16px/1.4 system-ui;margin:2rem}#log{margin-top:1rem;padding:.5rem;border:1px solid #ccc}</style>
</head>
<body>
  <h1>Local Server Check</h1>
  <button id="btn">Click me</button>
  <div id="log">Ready</div>
  <script>
    document.getElementById('btn').onclick = () => {
      const t = new Date().toLocaleTimeString();
      document.getElementById('log').textContent = 'Clicked at ' + t;
    };
  </script>
</body>
</html>
```

3. Install a static server. Terminal:

```bash
npm install -g http-server
```

If permission is denied on macOS or Linux, prefix with `sudo`.

4. Launch the server with caching disabled so edits appear on refresh. Terminal inside `node-map-lab`:

```bash
http-server . -p 8080 -c-1
```

5. Open the page. Browser to `http://localhost:8080/index.html`. Click the button. Confirm it updates the log.

6. Edit and refresh. Change any text in `index.html`, save in the editor, then refresh the browser.

---

### Task: host your map tiles HTML and live edit it

Goal: serve your map tiles HTML via Node, and confirm that edits to the generated HTML are reflected immediately while the server runs.

**Folder setup**

```
node-map-lab/
  map.html
```

**Steps**

1. Place your map HTML into the project. Copy the file you generate, for example `map.html`, into `node-map-lab`. If your page depends on a local tile server, start that before loading the page.

2. Optional tile server with Docker and TileServer GL.
   Folder structure:

```
node-map-lab/
  tiles/
    docker-compose.yml
    data/
      region.mbtiles
```

Terminal to create and run:

```bash
mkdir -p tiles/data
cp /path/to/your/region.mbtiles tiles/data/
cat > tiles/docker-compose.yml <<'YAML'
version: "3.8"
services:
  tileserver:
    image: maptiler/tileserver-gl
    volumes:
      - ./data:/data
    ports:
      - "8081:80"
YAML
cd tiles && docker compose up -d && cd ..
```

Tile endpoints will be under `http://localhost:8081`, for example `http://localhost:8081/styles/basic/style.json`.
Update any URLs inside `map.html` or its referenced `style.json` to point at your local server.

3. Run the Node server for your HTML. Terminal inside `node-map-lab`:

```bash
http-server . -p 8080 -c-1
```

4. Open the map. Browser to `http://localhost:8080/map.html`. Confirm tiles render. If blank, open the browser console and check URLs and CORS. Serving both HTML and tiles from the same host and port avoids CORS issues. If they must be on different ports, enable CORS on the tile server.

5. Live edit loop. Regenerate or edit `map.html` in your editor while `http-server` is running. Because `-c-1` disables caching, reloading the page shows changes immediately.

6. Optional auto reload. If you want automatic refresh on save, you can use `live-server` instead of `http-server`:

```bash
npx live-server --port=8080
```

Use only one server at a time.

---

### Challenge: add location and click interaction

Goal: extend your map to show the user location and allow clicking to create an interactive element at that point. No step by step. Implement using your renderer and style.

Requirements

* Show current position using the browser geolocation API.
* On map click, add a marker or open a popup that displays the clicked coordinates.
* Keep attribution visible if using OpenStreetMap or OpenMapTiles.

Hints and resources

* MDN Geolocation API: `navigator.geolocation.getCurrentPosition`, `watchPosition`.
* MapLibre GL JS events: `map.on('click', ...)`, and objects `maplibregl.Marker`, `maplibregl.Popup`.
* TileServer GL endpoints: `/styles/*/style.json`, `/data/*/{z}/{x}/{y}.pbf`, `/sprites`, `/fonts`.
* Common pitfalls: CORS when page and tiles are on different ports, stale cache, coordinate order must be [lng, lat].

Completion

Upload your code, and pictures or video of your program meeting the required functionality to this repository.

* Map loads from your Node server and displays tiles.
* A location marker appears and updates or the map centers on the reported position.
* Clicking the map creates a visible interactive element at the clicked coordinates.
