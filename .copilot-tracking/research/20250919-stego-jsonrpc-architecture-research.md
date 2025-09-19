<!-- markdownlint-disable-file -->
# Task Research Documents: Stego JSON-RPC over AI-Generated Images

Initial research scaffold. This document will evolve with deep analysis of implementing a JSON-RPC 2.0 transport tunneled through steganographic embedding inside Azure OpenAI generated images. Focus on: payload encoding pipeline (JSON-RPC -> serialize -> compress -> encrypt (future) -> header -> embed), extraction/validation, method dispatch, capacity planning, security, and extensibility for multiple embed strategies and fragmentation.

## Table of Contents
- [Scope and Success Criteria](#scope-and-success-criteria)
- [Outline](#outline)
- [Potential Next Research](#potential-next-research)
- [Research Executed](#research-executed)
  - [File Analysis](#file-analysis)
  - [Code Search Results](#code-search-results)
  - [External Research (Evidence Log)](#external-research-evidence-log)
  - [Project Conventions](#project-conventions)
- [Key Discoveries](#key-discoveries)
  - [Project Structure](#project-structure)
  - [Implementation Patterns](#implementation-patterns)
  - [Complete Examples](#complete-examples)
  - [API and Schema Documentation](#api-and-schema-documentation)
  - [Configuration Examples](#configuration-examples)
- [Technical Scenarios](#technical-scenarios)
- [Research Tools and Methods](#research-tools-and-methods)

## Scope and Success Criteria
- Scope: Architect a pluggable Python-based steganographic transport for JSON-RPC 2.0 using Azure OpenAI image generation as carrier source. Includes header format, embedding strategies (LSB now, DCT/palette roadmap), compression/encryption layering, fragmentation design, method dispatch patterns, capacity estimation, security/threat considerations, and performance trade-offs. Excludes UI/CLI polish beyond minimal encode/decode tooling; excludes non-image modalities.
- Assumptions:
  - Python 3.11+ target
  - Access to Azure OpenAI image generation with deterministic-ish prompts possible
  - Single-process server initially (can extend to async later)
  - No existing code in `src/` yet (greenfield)
  - JPEG-only carrier (PNG support deferred) simplifying capacity math to 3 color channels (8-bit) post-decoding
- Success Criteria:
  - Well-defined header spec with rationale & extensibility
  - Comparative evaluation of embedding strategies with chosen phased approach
  - Security model & mitigation recommendations (integrity, confidentiality, detectability)
  - Capacity planning formulas & examples vs carrier dimensions
  - Concrete implementation outline & preferred architecture selected

## Outline
1. Overview & Objectives
2. JSON-RPC Transport Requirements
3. Data Pipeline Stages
4. Steganographic Strategy Evaluation
5. Header & Fragmentation Design
6. Compression & Encryption Stack
7. Capacity & Performance Modeling
8. Security & Threat Model
9. Method Dispatch & Error Semantics
10. Architecture & Module Layout Proposal
11. Implementation Phasing Plan
12. Risks & Mitigations
13. Actionable Next Steps

### Potential Next Research
- Azure OpenAI image size/cost variance
  - **Reasoning**: Needed for capacity and performance modeling
  - **Reference**: README capacity planning need
- LZ4 vs zlib compression ratios on JSON payload shapes
  - **Reasoning**: Choose default compression algorithm
  - **Reference**: README mentions future lz4
- Steganalysis detection thresholds for LSB on high-variance images
  - **Reasoning**: Validate risk & need for pixel selection randomization
  - **Reference**: Security considerations section

## Research Executed

### File Analysis
- README.md
  - Provides high-level architecture, header draft, error mapping, roadmap (no code yet).
- src/ (empty)
  - Greenfield implementation space.

### Code Search Results
- (None yet; repository contains no source code to search.)

### External Research (Evidence Log)
- Steganography (Wikipedia) 2025-09-19
  - High-capacity carriers benefit from redundancy; LSB modification across RGB channels allows near-undetectable single-bit changes per channel when sparsely distributed.
  - Detection risk rises with higher encoding density producing statistical anomalies in least significant bits vs natural sensor/compression noise.
  - Source: https://en.wikipedia.org/wiki/Steganography
- Least Significant Bit (Wikipedia) 2025-09-19
  - LSB substitution minimally alters color values (difference often imperceptible for 1-bit change per channel); randomization of selection mitigates "too perfect" randomness blocks noted by steganalysis.
  - Source: https://en.wikipedia.org/wiki/Least-significant_bit
- JPEG Structure (Wikipedia) 2025-09-19
  - Lossy DCT quantization introduces compression artifacts; repeated re-encoding can destroy embedded LSB patterns—embedding should occur post-generation just before transmission; avoid additional lossy saves.
  - Progressive vs baseline JPEG difference minimal for our payload embedding if operating on raw pixel buffer pre-encoding (preferred: embed after image generation output—likely already JPEG/PNG).
  - Source: https://en.wikipedia.org/wiki/JPEG
- Azure OpenAI Image Generation (Docs) 2025-09-19
  - DALL-E 3 supported sizes: 1024x1024, 1792x1024 (landscape), 1024x1792 (portrait). Square generates faster.
  - `style`: vivid | natural (default vivid). `quality`: standard | hd (hd slower, finer detail). `n` fixed to 1 for DALL-E 3.
  - Response formats: `url` or `b64_json`. Provide base64 path for immediate in-process decode (no external fetch).
  - API streaming supports partial images (perceived latency improvement) via `stream=true` + `partial_images` (1-3).
  - GPT-image-1 (preview) also exists with different size set; out of initial scope (limiting to DALL-E 3 for now).
  - Source: https://learn.microsoft.com/en-us/azure/ai-foundry/openai/how-to/dall-e
  - Reference parameters enumerated in preview API: size, style, quality, response_format, user.

### Azure OpenAI Image API Sample (DALL-E 3)
```http
POST {AZURE_OPENAI_ENDPOINT}/openai/v1/images/generations?api-version=preview
Authorization: Bearer <API_KEY>
Content-Type: application/json

{
  "model": "dall-e-3",
  "prompt": "High variance abstract fiber optic lattice, rich detail, stochastic texture",
  "n": 1,
  "size": "1024x1024",
  "style": "vivid",
  "quality": "standard",
  "response_format": "b64_json"
}
```

Python fragment embedding pipeline (pseudo):
```python
import base64, httpx

def generate_carrier(prompt: str, *, endpoint: str, key: str) -> bytes:
    r = httpx.post(
        f"{endpoint}/openai/v1/images/generations",
        headers={"Authorization": f"Bearer {key}"},
        json={
            "model": "dall-e-3",
            "prompt": prompt,
            "n": 1,
            "size": "1024x1024",
            "style": "vivid",
            "quality": "standard",
            "response_format": "b64_json"
        }, timeout=60
    )
    r.raise_for_status()
    b64 = r.json()["data"][0]["b64_json"]
    return base64.b64decode(b64)
```

Latency considerations (qualitative initial hypothesis):
- Generation dominates end-to-end RPC; expected 2–8s standard quality (est. to refine with empirical logging). hd quality likely +30–70% latency.
- Stego embed/extract O(pixels); negligible (<50ms) relative to generation.
- Batch embedding reduces per-call amortized latency until capacity limit.

Observability integration requirements:
- Log structured events: {"phase": "generate|embed|extract|dispatch", "req_id", "method", "duration_ms", "payload_hash_prefix"}.
- Capture carrier size, estimated capacity, used capacity %, compression ratio (raw JSON bytes vs embedded bytes) for tuning.
- Add histogram metrics: image_generation_seconds, embed_bytes, extraction_validate_failures.
- Trace correlation: propagate optional external trace id in JSON-RPC params meta or header extension (future).

### Project Conventions
- Standards referenced: `.github/copilot-instructions.md` (priority rules for research placement in `.copilot-tracking/research/`).
- Instructions followed: Research file naming, template adaptation, exclusion from linting.

## Key Discoveries

### Project Structure
Early stage repository with conceptual README and empty `src/`. ADR directory exists but empty, indicating future architectural decision records to be populated (header, fragmentation, encryption, strategies).

### Implementation Patterns
Planned pluggable strategy interface for embedding; JSON-RPC compliance including batching and notifications; header contains integrity hash and fragmentation fields (future usage). Emphasis on deterministic, pure handlers and structured logging.

Stego Strategy Considerations (evidence-informed):
- LSB (current): Simple O(n) embed/extract, high capacity (~3 bits/pixel if using 1 bit/channel), low robustness vs recompression, detectability increases with density > ~50% of available LSBs.
- Planned DCT: Operates in frequency domain adjusting mid-frequency coefficients to balance imperceptibility and robustness to minor recompression (JPEG). Higher complexity (need block-wise quant table awareness) but improved resistance to naive statistical LSB tests.
- Palette (future): Suitable for limited-color images; not ideal for AI-generated high-variance photographs (palettes often large or absent). Lower priority.

JSON-RPC Implementation Patterns (Planned Research Direction):
- Minimal custom builder vs integrating existing lightweight library (decision leaning custom for tight control over error mapping & batch semantics + reduced deps).
- Batch handling: validate entire array first, then dispatch concurrently (future async) while accumulating results; preserve order only in output array if spec strictly requires (JSON-RPC states response array order is unspecified relative to request order—option to output in completion order or id-sorted; we will choose id-sorted stable mapping for deterministic testing).
- Error mapping table from README drives centralized exception taxonomy; define base `JsonRpcError(Exception)` with code/message/data.
- Input validation: enforce `jsonrpc == "2.0"`, presence of `method` (string), `params` optional (dict or list), `id` scalar (str|int) or absent (notification).

#### Canonical Serialization Strategy
Goal: Deterministic byte representation for hashing, integrity, and optional future signing/HMAC.

Rules:
1. Use `json.dumps` with: `separators=(",",":")`, `ensure_ascii=False`, `sort_keys=True`.
2. Disallow NaN/Infinity (`allow_nan=False`).
3. For batches: create list of per-request canonical objects (notifications included) in original order (even though response may reorder).
4. Hash input (pre-compression) bytes via SHA-256 → used for:
   - Header HASH (for unencrypted variant) or key derivation salt (encrypted variant).
5. Hash prefix (first 8 hex chars) for log correlation; do NOT truncate in header (full 32 bytes stored), enabling tamper detection.

Pseudo:
```python
def canonical_json(obj: Any) -> bytes:
    return json.dumps(obj, sort_keys=True, separators=(",",":"), ensure_ascii=False, allow_nan=False).encode("utf-8")
```

#### JSON-RPC Request Lifecycle (Single)
1. Build Python dict with enforced keys ordering only at serialization stage.
2. Canonical serialize.
3. Hash → hash_bytes.
4. (Optional) Compress.
5. (Optional future) Encrypt (AES-GCM); replace plaintext with ciphertext; associated data could include header static fields.
6. Construct header with payload length & hash.
7. Embed into carrier.

#### Batch Processing Algorithm
Validation Pass:
1. Confirm payload is JSON array (length >0) else `Invalid Request` (-32600).
2. Iterate elements; each must be valid request or notification object.
3. Accumulate two arrays: `requests_with_id`, `notifications`.
4. Capacity estimation (optional early reject) based on combined serialized size.

Dispatch Pass:
1. For each request with id: dispatch method handler.
2. Collect success → `{ "jsonrpc": "2.0", "result": <value>, "id": original_id }`.
3. Collect failures → `{ "jsonrpc": "2.0", "error": { code, message, data? }, "id": original_id }`.
4. After completion, sort responses by id using:
   - Numeric ids before string ids OR preserve original id type ordering? Simplicity: python tuple key `(isinstance(id, str), id)`.
5. If only notifications: no response payload produced (server returns `None` → caller can translate to HTTP 204).

Complexity: O(n) validation; dispatch cost dominated by handler time.

#### Error Taxonomy Consolidation
| Name | Code | Trigger | Retry Hint |
|------|------|---------|------------|
| ParseError | -32700 | JSON decode failure | Fix syntax |
| InvalidRequest | -32600 | Structural validation fail | Correct shape |
| MethodNotFound | -32601 | Unregistered method | Register / correct method |
| InvalidParams | -32602 | Param type/arity errors | Adjust params |
| InternalError | -32603 | Uncaught handler exception | Investigate bug |
| CapacityExceeded | -32001 | Payload exceeds embed capacity | Reduce batch / increase size |
| IntegrityFailed | -32002 | Hash mismatch after extraction | Resend / investigate tamper |
| FragmentError | -32003 | Fragment index/total mismatch | Resend sequence |

Python Exception Skeleton:
```python
class JsonRpcError(Exception):
    code: int
    message: str
    def __init__(self, message: str = None, data=None):
        if message: self.message = message
        self.data = data
    def to_obj(self, id_):
        err = {"code": self.code, "message": self.message}
        if self.data is not None: err["data"] = self.data
        return {"jsonrpc": "2.0", "error": err, "id": id_}

class ParseError(JsonRpcError): code=-32700; message="Parse error"
class InvalidRequest(JsonRpcError): code=-32600; message="Invalid Request"
class MethodNotFound(JsonRpcError): code=-32601; message="Method not found"
class InvalidParams(JsonRpcError): code=-32602; message="Invalid params"
class InternalError(JsonRpcError): code=-32603; message="Internal error"
class CapacityExceeded(JsonRpcError): code=-32001; message="Capacity exceeded"
class IntegrityFailed(JsonRpcError): code=-32002; message="Integrity check failed"
class FragmentError(JsonRpcError): code=-32003; message="Fragment error"
```

#### Deterministic Hashing with Batches
Option A (Selected): Hash the full canonical batch JSON string (array of requests) → single hash in header. Simpler integrity unit.
Option B (Rejected for v1): Per-element hashing then Merkle root (adds complexity not needed until fragmentation/ECC introduced).
Rationale: Atomic embed requirement; either entire batch fits or rejected.

#### Module Layout Proposal (Draft)
```
src/
  stego/
    __init__.py
    rpc/
      request_builder.py      # call(), notify(), batch(); canonical serialization
      errors.py               # JsonRpcError taxonomy
      dispatcher.py           # method registry + dispatch logic
      hashing.py              # canonical_json(), compute_hash()
    stego/
      header.py               # encode/decode header
      lsb_embed.py            # LSB strategy
      capacity.py             # estimation & occupancy policy
      encrypt.py              # (future) AES-GCM wrapper
    transport/
      client.py               # StegoRpcClient (sync)
      server.py               # StegoRpcServer / process_image()
    observe/
      metrics.py              # abstraction (optional backend)
      logging.py              # structured logging helpers
```

#### Open Questions (To Address Later)
- Should numeric vs string id sorting be documented as stable contract? (Yes; include in README for predictability.)
- Provide optional strict batch response order flag? (Could be environment variable, default deterministic id ordering.)
- Validate param schema? (Defer; potential JSON Schema plugin later.)

#### Alternative Libraries Considered (Not Selected)
- `json-rpc` / `jsonrpcserver`: add abstraction overhead; less control over low-level batch validation + custom error mapping; easier to roll minimal subset.
- `fastjsonrpc`: optimized but geared toward network frameworks; unnecessary for initial synchronous internal dispatch.
Decision: Custom minimal core (~200–300 LOC) improves inspectability & alignment with stego pipeline.

#### Logging & Tracing Fields (Expanded)
| Field | Example | Note |
|-------|---------|------|
| phase | embed | Lifecycle stage |
| req_id | 7f3c4a12 | 64-bit random per RPC batch |
| hash8 | a1b2c3d4 | First 8 hex of SHA-256 pre-compression |
| batch_count | 5 | Requests with id + notifications total |
| notif_only | true/false | Controls response suppression |
| capacity_pct | 0.27 | Used logical bytes / budget |
| compress_ratio | 0.58 | compressed_bytes / raw_canonical_bytes |
| duration_ms | 182 | Phase timing |
| carrier_size | 1024x1024 | Resolution chosen |

#### Example End-to-End (Pseudo) Flow – Batch
```python
raw_objs = build_batch(batch_spec)  # list of dicts
canon = canonical_json(raw_objs)
payload_hash = sha256(canon).digest()
compressed = zlib.compress(canon, level=6)
if len(compressed) > budget:
    raise CapacityExceeded()
header = encode_header(payload_hash, len(compressed), flags=compressed_flag)
stego_bytes = lsb_embed(carrier_image_bytes, header + compressed)
```

Extraction:
```python
data = lsb_extract(carrier_image_bytes)
header, payload = decode_header(data)
if sha256(payload[header.payload_slice]).digest() != header.hash:
    raise IntegrityFailed()
decompressed = zlib.decompress(payload)
batch_objs = json.loads(decompressed)
responses = dispatcher.dispatch(batch_objs)
if responses:
    # mirror encode path for response JSON-RPC result objects
    ...
```

Observability Hooks Integration Plan:
- Decorator `@instrument_phase(name)` wraps embed/extract/dispatch for timing and structured log emission.
- Hash prefix: first 8 hex chars of SHA-256 of canonical serialized request (pre-compression) for correlation without leaking payload.
- Optional metrics backend abstraction (simple interface `.inc(name, **labels)` / `.observe(name, value, **labels)`).

### Complete Examples
```python
# Placeholder for future end-to-end encode/decode demonstration once modules exist.
```

### API and Schema Documentation
JSON-RPC 2.0 spec assumptions: fields jsonrpc, method, params, id, result, error; batch semantics; reserved error codes mapping table provided.

### Configuration Examples
```text
AZURE_OPENAI_ENDPOINT, AZURE_OPENAI_API_KEY, AZURE_OPENAI_DEPLOYMENT,
STEGO_IMAGE_PROMPT, STEGO_EMBED_STRATEGY, STEGO_COMPRESSION, STEGO_HASH_ALG,
STEGO_MAX_PAYLOAD_BYTES
```

## Technical Scenarios
### Embedding Strategy Selection
Will compare LSB vs DCT vs Hybrid (LSB with randomized sparse distribution + optional error-correcting code (ECC)).

### Fragmentation Protocol
Design of multi-image sequencing with integrity & reassembly ordering constraints.

### Encryption Integration
Placement of AES-GCM after compression prior to header hashing; key derivation & optional HMAC vs built-in AEAD tag usage.

### Dispatcher Design
Synchronous vs async; middleware hooks for logging & metrics; error mapping enforcement.

### Capacity Planning (Initial Formulas)
Let carrier resolution = W x H (e.g., 1024 x 1024 = 1,048,576 pixels).

LSB single-bit-per-channel capacity (no ECC, no fragmentation):
```
usable_bits = W * H * channels * bits_per_channel_used
channels = 3 (RGB)
bits_per_channel_used = 1 (conservative)
usable_bytes_raw = usable_bits // 8
```
Example 1024x1024: 1,048,576 * 3 * 1 / 8 = 393,216 bytes (~384 KiB) BEFORE header/hash/compression.

Safety margin (to reduce detection risk) e.g. use occupancy factor f (default 0.35):
```
payload_budget = floor(usable_bytes_raw * f) - header_len
```
Header length (current spec): 4+1+1+1+1+1+4+32 = 46 bytes (no fragments). Round to 48 for alignment.

With f=0.35: budget ≈ 137,725 bytes (~134.5 KiB). Compression (zlib) expected 30–60% savings on JSON with structural repetition, boosting effective logical JSON size supported.

Capacity vs Non-Square Sizes:
- 1792x1024 → 1,835,008 px → raw 688,128 bytes → budget (~0.35) ≈ 240 KiB.
- 1024x1792 identical pixel count.

Future fragmentation: if payload > budget, split across sequential images with FRAG_TOTAL >1; each fragment except last carries same header size; introduce fragment hash chaining (optional) for ordering integrity.

### Encryption Integration (Preview Note)
AES-GCM over compressed payload; header HASH field calculated over ciphertext; add flag bits for encryption & future HMAC (if separate authenticity decision). Nonce derivation: HKDF(payload_hash || id || counter) truncated to 96 bits.

## Research Tools and Methods
Internal: repository file listing & analysis (no code yet). External: (pending) authoritative sources on steganography algorithms, JSON-RPC spec, compression performance, Azure OpenAI image generation parameters.
