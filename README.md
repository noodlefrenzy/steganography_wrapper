# Steganography Wrapper for JSON-RPC 2.0

> Tunnel JSON-RPC 2.0 requests, notifications, batch calls, and responses through AI-generated images using Azure OpenAI + steganographic embedding.

## Overview

This project provides a Python client/server transport wrapper that conceals JSON-RPC 2.0 traffic inside images. Instead of sending JSON over HTTP directly, the client:

1. Builds a JSON-RPC 2.0 request object (`{"jsonrpc":"2.0","method":"...", ...}`)
2. Serializes (optionally compresses / encrypts) it
3. Embeds the bytestream into an Azure OpenAI–generated image
4. Sends only the image to the server

The server:

1. Extracts & validates the JSON-RPC payload (single, notification, or batch)
2. Dispatches to registered method handlers
3. Wraps results (or errors) into JSON-RPC response objects
4. Re-embeds the response JSON (unless notification-only) into a new image
5. Returns the response image to the client which decodes and yields `result`(s) / raises on JSON-RPC `error`.

Outcome: a covert, protocol-faithful transport preserving JSON-RPC semantics (methods, params, `id`, notifications, batching) while making traffic appear as benign image exchange.

## Why JSON-RPC

- Minimal envelope, easy to embed & validate
- Native batch request and notification support
- Deterministic structure simplifies hashing & integrity checks
- Payload-agnostic stego layer (no need to reinterpret fields)

## Key Features

- Full JSON-RPC 2.0 support: request, response, notifications, batch
- Method registry + dispatcher abstraction
- Batch requests embedded in a single carrier image (capacity aware)
- Optional compression (`zlib`, future `lz4`), future encryption hook
- Integrity (length + hash) header to detect tampering/corruption
- Multiple embed strategies (LSB now; DCT/palette planned)
- Azure OpenAI image generation adapter (prompt/style configurable)
- Transport-agnostic design (HTTP baseline, extensible to S3/WebSocket)
- Stego header ready for future fragmentation & encryption flags

## JSON-RPC 2.0 Primer

| Field | Purpose |
|-------|---------|
| `jsonrpc` | Must be `"2.0"` |
| `method` | Procedure name (string) |
| `params` | Positional (array) or named (object), optional |
| `id` | Scalar (string/number); omitted for notifications |
| `result` | Success value in response |
| `error` | Object `{ code, message, data? }` on failure |
| Batch | Array of request objects (mix of requests + notifications) |

Notifications (no `id`) do not produce a response. Batch responses contain only entries for those with an `id`.

## High-Level Flow (Single Call)

1. `client.call("getUser", {"id":42})`
2. Build JSON-RPC request object
3. Serialize → optional compress → add header → embed → generate image
4. Send image → server decodes & validates JSON-RPC shape
5. Dispatch method → produce JSON-RPC response
6. Embed response → return image → client extracts → yield result

Notifications stop at step 5 (no response image). A pure-notification batch yields HTTP 204 (or empty body) per implementation policy.

## Architecture

```text
Client App
  -> Stego JSON-RPC Client
    -> JSON-RPC Builder (call / notify / batch)
      -> Serializer + (Compression/Encryption)
        -> Stego Header + Embed Strategy
          -> Azure OpenAI (carrier image)
            -> Transport (HTTP)
              -> Stego JSON-RPC Server
                -> Extract + Validate
                  -> Dispatcher (method registry)
                    -> Handlers
                  <- JSON-RPC Response(s)
                -> Serialize + Header + Embed
              <- Response Image (unless notifications only)
    -> Decoder -> JSON-RPC result/error raised/returned
```

### Components

- Stego JSON-RPC Client: Public interface: `call`, `notify`, `batch`
- Embed Strategy: Hides payload bits in carrier image
- Azure OpenAI Adapter: Retrieves/generates base image
- Header Codec: Packs metadata (magic, version, flags, len, hash, fragments)
- Dispatcher: Maps method names → Python callables
- Server Wrapper: Orchestrates decode → dispatch → encode
- Capacity Planner: Verifies payload fits chosen carrier dimensions

## Installation

> Packaging TBD. Clone for now.

```powershell
git clone https://github.com/your-org/steganography_wrapper.git
cd steganography_wrapper
python -m venv .venv
. .\.venv\Scripts\Activate.ps1
pip install -U pip
pip install -r requirements.txt
```

When published:

```powershell
pip install stego-jsonrpc
```

## Configuration (Environment)

```powershell
$env:AZURE_OPENAI_ENDPOINT    = "https://YOUR-RESOURCE.openai.azure.com/"
$env:AZURE_OPENAI_API_KEY     = "<key>"
$env:AZURE_OPENAI_DEPLOYMENT  = "<image-deployment>"  # e.g. dall-e-3
$env:STEGO_IMAGE_PROMPT       = "fractal abstract texture high variance"
$env:STEGO_EMBED_STRATEGY     = "lsb"                  # lsb | dct | palette (future)
$env:STEGO_COMPRESSION        = "zlib"                 # none | zlib | lz4
$env:STEGO_HASH_ALG           = "sha256"
$env:STEGO_MAX_PAYLOAD_BYTES  = "65536"
```

## Usage Examples

### Single Call

```python
from stego_wrapper import StegoRpcClient

client = StegoRpcClient(
    image_prompt="hyper-detailed fiber optic weave",
    embed_strategy="lsb",
    compression="zlib"
)

user = client.call("getUser", {"id": 42})
print(user)
```

### Notification (No Response Image)

```python
client.notify("heartbeat", {"ts": "2025-09-19T16:20:00Z"})
```

### Batch (Mixed Requests + Notifications)

```python
batch = [
    {"method": "getUser", "params": {"id": 42}, "id": "u1"},
    {"method": "heartbeat", "params": {"ts": "2025-09-19T16:21:00Z"}},  # notification
    {"method": "listOrders", "params": {"userId": 42}, "id": "orders1"}
]

responses = client.batch(batch)
print(responses["u1"], responses["orders1"])
```

### Error Handling

```python
try:
    data = client.call("getUser", {"id": "not-an-int"})
except StegoJsonRpcError as e:
    print(e.code, e.message, e.data)
```

### Server (Pseudo)

```python
from stego_wrapper import StegoRpcServer

server = StegoRpcServer()

@server.method("getUser")
def get_user(id: int):
    return {"id": id, "name": "Alice"}

@server.method("listOrders")
def list_orders(userId: int):
    return [{"orderId": 7, "total": 12345}]

def stego_endpoint(image_bytes: bytes) -> bytes | None:
    return server.process_image(image_bytes)
```

## Internal Header (Proposed)

| Field | Size | Notes |
|-------|------|-------|
| MAGIC | 4 | `STEG` |
| VER | 1 | 0x01 |
| FLAGS | 1 | bit0=compressed, bit1=encrypted, bit2=fragmented |
| ALG | 1 | hash id (0x01=SHA256) |
| FRAG_TOTAL | 1 | 1 if not fragmented |
| FRAG_INDEX | 1 | 0 if single fragment |
| PAYLOAD_LEN | 4 | Big-endian |
| HASH | 32 | SHA-256(payload) |
| PAYLOAD | n | JSON-RPC JSON (single or batch) |

Fragmentation (future) permits spanning large batches over multiple images.

## Error Mapping

| Condition | JSON-RPC Code | Message | HTTP Status |
|-----------|---------------|---------|-------------|
| Parse error (JSON decode) | -32700 | Parse error | 400 |
| Invalid Request shape | -32600 | Invalid Request | 400 |
| Method not found | -32601 | Method not found | 404 |
| Invalid params | -32602 | Invalid params | 422 |
| Internal handler error | -32603 | Internal error | 500 |
| Capacity exceeded | -32001 | Capacity exceeded | 413 |
| Integrity hash mismatch | -32002 | Integrity check failed | 422 |
| Fragment error | -32003 | Fragment error | 400 |

Application-specific errors may choose custom codes outside reserved range.

## Steganography Strategy

Default: LSB substitution across pseudo-random pixels (seed derived from payload hash) to disperse changes. Future strategies (DCT coefficient tweaks, palette perturbation) will implement a common interface:

```python
class EmbedStrategy:
    def capacity(self, image) -> int: ...
    def embed(self, image, data: bytes) -> bytes: ...
    def extract(self, image) -> bytes: ...
```

## Security Considerations

- Add encryption layer (planned): compress → encrypt (AES-GCM) → header over ciphertext → embed
- Randomize pixel selection to mitigate steganalysis
- High-variance prompts reduce statistical anomalies
- No logging of raw JSON-RPC payloads; log method names + hash prefix + duration
- Optional HMAC (future) for authenticity
- Rate limit high-frequency notification abuse

## Performance Notes

- Azure image generation dominates latency (consider a carrier image cache pool)
- Batch calls amortize image cost (balance against capacity/detectability)
- Compression helpful for many small batch entries

## Limitations

- Fragmentation not yet implemented (large batches must fit one image)
- Notifications still incur image generation cost (batch them)
- Not for ultra low-latency interactive RPC
- Steganalysis risk if deterministic patterns used

## Roadmap

- Multi-image fragmentation/reassembly
- AES-GCM encryption + HMAC signing
- Adaptive carrier selection & capacity estimator
- Additional embed strategies (DCT, palette)
- Image reuse / delta embedding
- Async client & streaming/WebSocket mode
- CLI tooling (encode/decode/header inspect)
- Statistical steganalysis test harness
- Method param JSON Schema validation integration

## Contributing

Please read `CONTRIBUTING.md` for full guidelines.

Quick path:

1. Fork
1. Branch `feat/<short-description>`
1. Add/extend tests (header, strategy, dispatcher)
1. Run linters / formatters
1. PR: include capacity + security notes

By participating you agree to the project `CODE_OF_CONDUCT.md`.

### Development Guidelines

- Keep handlers pure & deterministic
- No raw payload logging; structured logs only
- Deterministic extraction independent of external state
- Constant-time comparisons for hashes (when security mode enabled)

## ADRs

Architectural decisions live in `docs/ADR/` (add for: header format, fragmentation protocol, encryption design, strategy interfaces).

## License

Licensed under the MIT License - see `LICENSE` file.

SPDX-Identifier: MIT

## Disclaimer

Research & educational use. Steganography alone does not provide confidentiality—combine with encryption for secure use cases.

