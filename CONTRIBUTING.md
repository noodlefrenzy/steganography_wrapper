# Contributing

Thank you for your interest in contributing! This project implements a steganographic transport for JSON-RPC 2.0; correctness, security, and reproducibility are priorities.

## TL;DR Workflow

1. Open (or find) an issue describing the change
2. Fork + create branch: `feat/<short-description>` or `fix/<issue-id>`
3. Add tests (or update existing)
4. Make changes (small, focused commits)
5. Run formatting / lint / tests locally
6. Submit PR referencing the issue; fill in template items (threat surface, capacity impact)

## Branching & Commit Guidelines

- Keep branches short-lived
- Use conventional-ish commit messages where possible:
  - `feat: add dct embed strategy`
  - `fix: correct hash length validation`
  - `docs: update header layout`
  - `test: add fragmentation unit cases`
- Rebase (not merge) from `main` before finalizing a PR when feasible

## Code Style

- Python: prefer type hints everywhere (PEP 484)
- Follow standard formatting (e.g., Black) and linting (Ruff / Flake8) if config is added
- Keep functions small & purposeful; avoid side effects in embedding/extraction logic

## Testing

Suggested test categories:

- Header codec (round-trip encode/decode, corruption detection)
- Embed strategy capacity calculations (edge sizes, near-limit payloads)
- JSON-RPC dispatcher (single, batch, notifications, error codes)
- Integrity failure scenarios (hash mismatch, truncated payload)
- Performance sanity (payload size vs. image generation stub)

## Security & Privacy

- Never log full payload bytes or secrets
- Redact or hash (first 8 hex of SHA-256) for correlation
- Avoid timing side channels in comparison (use constant-time compare if adding MAC/HMAC)
- Validate all external inputs *before* dispatch

## Adding an Embed Strategy

1. Implement `EmbedStrategy` interface
2. Provide `capacity`, `embed`, `extract` with deterministic behavior
3. Add tests: capacity correctness, full round-trip, malformed extraction handling
4. Document any trade-offs (detectability, image quality impact)

## Error Codes

If adding new JSON-RPC custom error codes, keep them outside the reserved core set and document in README error mapping.

## Documentation

- Update `README.md` for new features or configuration keys
- Add ADR in `docs/ADR/` for significant architectural changes:
  - Filename format: `NNN-short-title.md`
  - Include context, decision, alternatives, consequences

## Pull Request Checklist

- [ ] Linked issue / rationale provided
- [ ] Tests added / updated and pass locally
- [ ] Documentation updated
- [ ] Security considerations noted
- [ ] Capacity impact (approx max added bytes) stated
- [ ] No secret material committed

## Release Management (Future)

When versioning begins:

- Use semantic versioning: MAJOR.MINOR.PATCH
- Changelog entries grouped by Added / Changed / Fixed / Security
- Tag releases with signed annotated tags

## Getting Help

Open an issue with label `question` or start a discussion thread (if enabled). Provide logs (sanitized), environment (Python version, OS), and reproduction steps.

We appreciate every contribution—thank you!
