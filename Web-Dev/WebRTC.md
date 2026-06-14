---
id: a1b2c3d4-1126-4000-8000-000000000126
title: WebRTC
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001126
---

**Links**: [[WebSocket Deep Dive]] | [[WebSockets]] | [[HTTP-3 and QUIC]] | [[HTTP Protocol]] | [[Web Development Fundamentals]] | [[Desktop Apps with Electron and Tauri]]


# WebRTC

WebRTC (Web Real-Time Communication) enables peer-to-peer audio, video, and data sharing between browsers without plugins or intermediaries.

## Architecture

```
Peer A               Signaling Server               Peer B
  │                        │                          │
  │── Offer (SDP) ────────►│── Forward Offer ────────►│
  │◄── Answer (SDP) ──────│◄── Forward Answer ───────│
  │── ICE Candidates ─────►│── ICE Candidates ───────►│
  │◄── ICE Candidates ────│◄── ICE Candidates ───────│
  │                        │                          │
  │◄══════ STUN/TURN (NAT traversal) ═══════════════►│
  │◄════════════ Direct P2P (SRTP/SCTP) ════════════►│
```

## Signaling

```javascript
// Peer A — create and send offer
const pc = new RTCPeerConnection(config);

pc.onicecandidate = (e) => {
    if (e.candidate) {
        signaling.send({ type: "candidate", candidate: e.candidate });
    }
};

pc.createOffer().then((offer) => {
    return pc.setLocalDescription(offer);
}).then(() => {
    signaling.send({ type: "offer", sdp: pc.localDescription });
});

// Peer B — receive offer, create answer
signaling.onmessage = async (msg) => {
    if (msg.type === "offer") {
        await pc.setRemoteDescription(msg.sdp);
        const answer = await pc.createAnswer();
        await pc.setLocalDescription(answer);
        signaling.send({ type: "answer", sdp: answer });
    }
};
```

## STUN vs TURN

| Server | Purpose | Cost |
|--------|---------|------|
| STUN | Discover public IP/port for NAT traversal | Free |
| TURN | Relay media when P2P fails (symmetric NAT) | Bandwidth cost |

```javascript
const config = {
    iceServers: [
        { urls: "stun:stun.l.google.com:19302" },
        {
            urls: "turn:turn.example.com:3478",
            username: "user",
            credential: "pass"
        }
    ]
};
```

**TURN failure modes**: Without TURN, ~15% of connections fail (symmetric NAT). TURN adds latency and bandwidth cost.

## Media Tracks

```javascript
// Get user media
const stream = await navigator.mediaDevices.getUserMedia({
    video: { width: 1280, height: 720, frameRate: 30 },
    audio: { echoCancellation: true, noiseSuppression: true }
});

// Add to peer connection
stream.getTracks().forEach(track => pc.addTrack(track, stream));

// Screen sharing
const screenStream = await navigator.mediaDevices.getDisplayMedia({
    video: { cursor: "always" },
    audio: false
});

// Receive remote stream
pc.ontrack = (event) => {
    remoteVideo.srcObject = event.streams[0];
};
```

## Data Channels

```javascript
// Create data channel
const dc = pc.createDataChannel("chat", {
    ordered: false,       // unordered delivery (faster)
    maxPacketLifeTime: 3000  // ms before dropping
});

dc.onopen = () => console.log("Channel open");
dc.onmessage = (e) => console.log("Received:", e.data);

dc.send(JSON.stringify({ type: "message", text: "Hello!" }));
```

| Option | ordered | maxRetransmits | Use Case |
|--------|---------|----------------|----------|
| Reliable | true | (default) | File transfer |
| Unreliable, retry | false | 5 | Position updates |
| Unreliable, timeout | false | (maxPacketLifeTime) | Voice data |

## Connection States

```javascript
pc.oniceconnectionstatechange = () => {
    console.log("ICE state:", pc.iceConnectionState);
    // "new" → "checking" → "connected" → "completed" (success)
    // Or: "failed" → "disconnected" → "closed"
};

pc.onconnectionstatechange = () => {
    if (pc.connectionState === "connected") {
        console.log("P2P connection established");
    }
};
```

## Codec Support

| Codec | Type | Bitrate | Licensing |
|-------|------|---------|-----------|
| VP8 | Video | Flexible | Royalty-free |
| VP9 | Video | Better compression | Royalty-free |
| H.264 | Video | Hardware acceleration | Patent-encumbered |
| AV1 | Video | Best compression | Royalty-free |
| Opus | Audio | 6-510 kbps | Royalty-free |

## Libraries

| Library | Purpose |
|---------|---------|
| PeerJS | Simplified WebRTC wrapper |
| SimplePeer | Easy P2P connections |
| LiveKit | Production SFU (Selective Forwarding Unit) |
| Mediasoup | SFU for large-scale conferences |
| Jitsi | Full video conferencing solution |

## SFU (Selective Forwarding Unit)

For group calls (3+), use a server to forward streams:

```
Each peer sends 1 stream → SFU → Each peer receives N-1 streams
(N = participants, upload = 1, download = N-1)
```

**Alternatives**: MCU (mixes streams, higher server cost, lower client cost).

**Links**: [[HTTP Protocol]] | [[Web Development Fundamentals]] | [[WebSockets]] | [[Real-Time Communication]] | [[Video Streaming]]
