### Hi, I'm Patrick McCoy

Software engineer and builder in Boulder County, Colorado. I spent six years building streaming telemetry systems at SolidFire and NetApp, then started and now run a custom home building company. These days I write Scala and Python for real-time sensor and RF systems.

**Background:** Senior Software Engineer at NetApp/SolidFire, where I was one of two founding engineers on Active IQ. It started as PHP and became a Scala + Kafka streaming platform ingesting telemetry from 2,500+ storage clusters worldwide, which we moved from a colo to AWS.

---

#### Current work (private repositories)

**Real-time ADS-B airspace monitoring and conflict prediction**
`Scala 3` `Apache Pekko (Typed Actors, Streams, HTTP)` `Percona Server for MySQL` `Slick` `React 19` `TypeScript` `Leaflet` `Prometheus` `Grafana`

- Ingests SBS-1 (BaseStation) message streams from multiple SDR receivers over TCP. Pekko Streams handles framing and parsing, and a typed actor hierarchy keeps one actor per receiver and one per aircraft.
- Fuses all receivers into one live air picture and runs a periodic pairwise **closest-point-of-approach (CPA)** scan. A TCAS-style **TAU + DMOD** time and distance test flags predicted conflicts. Proximity and predicted-conflict encounters are persisted with running minima.
- Uses great-circle geometry throughout: haversine distance, bearings, destination points, cross-track distance, and great-circle path intersection with projected altitude at the crossing.
- A **flight-phase state machine** uses dwell-time smoothing, ground-bit plausibility checks and a legal-transition graph to handle noisy ADS-B data.
- **Loiter and holding-pattern detection** works from cumulative signed heading change, bounded by distance from the turn's centroid.
- A **spatial grid index** handles radius queries over about 80,000 airports, cross-validated against brute force in tests.
- Writes about 145M track rows per month into monthly-partitioned tables (migrated from MariaDB to Percona Server for better partition management). Batched writers survive multi-hour table locks without losing data by backing off, re-queuing failed batches, and spilling to an on-disk binary spool that replays when writes resume.
- A database watchdog shuts down cleanly so systemd can restart the process. Webhook alerting is rate-limited and deduplicated. Prometheus metrics feed a Grafana dashboard.
- Includes a REST + Server-Sent Events API and a live React/Leaflet map. Testing covers about 1,300 ScalaTest cases plus Vitest/Playwright, and CI runs on GitHub Actions. It ships as a `.deb`.

**Passive RF drone detection and Remote ID decoding**
`Python` `asyncio` `RTL-SDR` `NumPy` `Scapy` `Bleak` `FastAPI` `WebSockets` `SQLite` `Raspberry Pi 5`

- Decodes FAA Remote ID (ASTM F3411 / OpenDroneID) from Wi-Fi NAN beacons captured in monitor mode and from Bluetooth 5 advertising.
- Runs wideband RF anomaly detection on an RTL-SDR across 433, 868 and 915 MHz, using IQ capture, a 1024-point FFT power spectrum, and peak and bandwidth detection.
- A time- and band-based correlator classifies each contact as *confirmed*, *Remote ID only*, or *RF with no Remote ID*, the last being a potentially non-compliant drone.
- All sensors feed an async event bus, with a live WebSocket dashboard, webhook alerts and persistent event logging.

**Local GPU transcription and speaker diarization**
`Python` `PyTorch` `CUDA` `OpenAI Whisper` `pyannote.audio` `Flask` `Prometheus`

- Runs speech-to-text and speaker diarization entirely on self-hosted NVIDIA hardware, with no cloud inference.
- Ships as a CLI, a web app with live progress streaming, and polling workers that scale horizontally by claiming jobs from a coordination API.
- Sizes the model to available VRAM, serializes GPU access through configurable concurrency slots, and falls back to CPU on CUDA out-of-memory.
- Prometheus metrics feed a Grafana dashboard tracking stage latency, GPU slot wait, VRAM use and real-time factor. Deploys as a systemd Debian package.

**[mapfun](https://github.com/patrickmccoy/mapfun): graph routing algorithms from scratch** (public)
`Scala 3` `OpenStreetMap` `Neo4j` `Apache AGE`

- Uses a zero-allocation compressed-sparse-row graph with Dijkstra (indexed decrease-key heap), A\*, bidirectional Dijkstra, Bellman-Ford, Floyd-Warshall, Yen's k-shortest paths and Tarjan's SCC.
- Routes on real OpenStreetMap road networks using two-pass PBF import and junction contraction, with the same traversals implemented in Neo4j (Cypher) and PostgreSQL (Apache AGE).
- 87 tests cross-validate the algorithms against each other.

**Multi-tenant construction management platform**
`Scala 3` `Cats Effect` `http4s` `Doobie` `PostgreSQL` `React`

- SaaS for custom-home builders covering cost codes, budgets, schedules, selections, change orders and two-way QuickBooks Online sync, plus a built-in AI agent and an MCP server.

---

#### Toolbox
Scala · Python · TypeScript/React · Pekko · Kafka · PyTorch/CUDA · PostgreSQL/MySQL · Neo4j · AWS · Docker · Linux · SDR/RF
