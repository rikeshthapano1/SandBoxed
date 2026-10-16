Added the initial scaffolding to enable peer-to-peer multiplayer via WebRTC:

Cloudflare Worker signaling server (signaling/)

Added wrangler.toml (Durable Object binding ROOM, class Room, v1 migration)
Added src/index.js - minimal WebSocket signaling server using Durable Objects
Creates rooms via POST /create → returns short roomId
WebSocket endpoint at /room/:roomId?playerId=...&role=host|player
Host registers on connect; players register and notify host (player-joined)
Routes signaling messages: offer (player→host), answer (host→player), ice (both directions) via player/host mapping
Basic CORS handling for OPTIONS + JSON responses
WebRTC test harness (public/webrtc-test/index.html)

Simple UI to test host/join flow across machines: name input, create room (host), join room (player), chat via WebRTC data channel
Configurable signaling endpoints at top of script:
SIGNALING_URL (WebSocket, e.g. ws://127.0.0.1:8787 or wss://...)
API_URL (HTTP, e.g. http://127.0.0.1:8787/ or https://...)
Host flow: creates room via POST /create, connects to /room/?role=host, accepts player offers, creates RTCPeerConnection per player, sends answers, exchanges ICE, sets up per-player data channel for broadcast
Player flow: connects to /room/?role=player, receives offer from host, answers, exchanges ICE, connects data channel
Logs connection state and relays messages between all peers (host broadcasts to others)
start frontend :
cd public
python -m http.server 8000

start signaling :
cd signaling
npx wrangler dev --port 8787
