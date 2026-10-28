# LDR World Dashboard — Render Deployment

## Deploy
1. Upload the contents of this folder to a GitHub repository.
2. Create a Render Web Service from that repository.
3. Build command: `pip install -r requirements.txt`
4. Start command: `python main.py`
5. Ensure the service has persistent storage if account/config files must survive redeploys.

## Dashboard metrics
The dashboard shows loaded account totals, online/offline estimates, and best-effort events parsed from the last 500 retained console log lines. These are not authoritative worldwide Free Fire totals; events not written to the logs cannot be counted. Process runtime and in-memory log analytics reset on restart.

## BR / CS selector
The BR / CS option is saved as a configuration preference. It only changes actual in-game behavior if the underlying protocol implementation supports that mode; the dashboard cannot guarantee a mode switch unsupported by the game protocol.

## Notes
- `Procfile` and `render.yaml` are included.
- Account credentials are stored locally in `accounts.txt`; do not publish a public repository containing real credentials. Use a private repository and secure secrets.


## Additive join telemetry endpoints

The existing bot code, root files, account files, and `Pb2/` contents are retained. Added endpoints:
- `GET /health` — uptime, memory, thread count, telemetry status JSON
- `GET /api/metrics` — rolling per-second/per-minute metrics
- `GET /api/summary` — recent minute summaries
- `GET /join-monitor` — separate dark live monitor; original `/` dashboard remains unchanged
- `GET /download-logs` — CSV export
- `POST /api/joins` — authenticated join event ingestion

Set `JOIN_API_TOKEN` in Render/Railway environment variables. Send actual join events with `Content-Type: application/json`, `X-API-Key: YOUR_TOKEN`, and JSON such as `{"player_id":"player-123","source":"event-source","metadata":{"region":"BD"}}`. Bearer authentication is also accepted. Do not publish account credentials or tokens in a public repository.

The counter counts events delivered to `/api/joins`; the external event-producing system must report each actual join. Existing log-based metrics remain as before. Render free storage can be ephemeral, so CSV persistence across restarts is not guaranteed without persistent storage. Existing `python main.py` startup is retained because the app uses its custom HTTP server.
