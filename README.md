# Self-Hosted Routing with Valhalla and Docker

Run your own routing server using [Valhalla](https://github.com/valhalla/valhalla) and Docker. Request routes between two points and visualize them as polylines on an interactive map. No Google Maps API needed.

📖 **Full tutorial:** [Self-Hosted Routing with Valhalla and Docker — From Setup to Polyline on a Map](LINK_TO_YOUR_DEVTO_ARTICLE)

## Quick Start

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop) installed and running
- Python 3.8+
- ~2-4 GB free disk space

### 1. Clone and enter the repo

```bash
git clone https://github.com/YOURUSERNAME/self-hosted-routing-valhalla.git
cd self-hosted-routing-valhalla
```

### 2. Install Python dependencies

```bash
pip install requests folium polyline
```

### 3. Start the Valhalla server

```bash
docker run -dt --name valhalla \
  -p 8002:8002 \
  -v "$(pwd)/data":/custom_files \
  ghcr.io/gis-ops/docker-valhalla/valhalla:latest
```

On Windows PowerShell, replace `"$(pwd)/data"` with `"${PWD}/data"`.

First run takes 2-5 minutes to build routing tiles. Watch the progress:

```bash
docker logs -f valhalla
```

Press `Ctrl+C` to stop watching — the server keeps running in the background.

### 4. Verify the server is running

```bash
curl http://localhost:8002/status
```

You should see a JSON response with `"route"` in the available actions.

### 5. Run the visualization

```bash
python show_route.py
```

Open `route.html` in your browser. You'll see the route drawn on an interactive map.

## Project Structure

```
self-hosted-routing-valhalla/
├── data/
│   └── socal-latest.osm.pbf    # OpenStreetMap data (SoCal region)
├── show_route.py                # Python script — requests route and renders polyline
├── LICENSE
└── README.md
```

## Managing the Server

```bash
docker stop valhalla       # stop the server
docker start valhalla      # restart (fast — tiles already built)
docker rm valhalla         # remove the container (tiles persist on disk)
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `docker: command not found` | Make sure Docker Desktop is running. Use system terminal, not VS Code's. |
| `Container name already in use` | Run `docker rm -f valhalla` then try again. |
| `Port 8002 already allocated` | Another service is using the port. Stop it or change `-p 8002:8002` to `-p 8003:8002`. |
| Route renders in wrong location | Check that `polyline.decode()` uses precision `6`, not `5`. |
| Tiles take too long to build | Add `-e CONCURRENCY=4 -e build_elevation=False` to the docker run command. |

For detailed troubleshooting, see the [full tutorial](LINK_TO_YOUR_DEVTO_ARTICLE).

## Using a Different Region

The included PBF covers Southern California. To use a different area:

1. Download a PBF from [Geofabrik](https://download.geofabrik.de/) or [BBBike](https://extract.bbbike.org/)
2. Place it in the `data/` directory
3. Update the coordinates in `show_route.py` to match your region
4. Restart the server with `-e force_rebuild=True`

See the companion guide: [How to Get the Right OpenStreetMap PBF File for Any Region](LINK_TO_YOUR_PBF_ARTICLE)

## License

[MIT](LICENSE)
