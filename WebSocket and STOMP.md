
## Websocket

communication protocol that creates a persistent and two way connection between client and server

HTTP
Client → Request → Server
Client ← Response ← Server
Connection ends

Websocket
Client <- Websocket Connection -> Server
messages can go either way, whenever they want

#### uses
-chat apps
-live notifs
-multiplayer games
-stock updates
-real time dashboards
-collabeditor

## STOMP
Simple text oriented Messaging protocol
Runs on top of Websocket

STOMP->Websocket->TCP->Internet



