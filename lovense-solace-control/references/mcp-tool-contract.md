# Proposed Lovense MCP Tool Contract

This document describes a suggested interface between Odysseus skills and a Lovense controller MCP server. These are **proposed tool names and schemas**, not existing tools. Adapt the skill to the actual connected server if its interface differs.

## Suggested tools

### `lovense_get_toys`

Discover connected toys.

Suggested result fields:
- `toys[]`
  - `id`: stable controller toy identifier
  - `name`: user-facing device name
  - `model`: reported model, where available
  - `connected`: current connection state
  - `capabilities[]`: operations reported as supported

### `lovense_get_status`

Input:
- `toy_id`: required target identifier

Suggested result:
- `toy_id`
- `connected`
- `status`: controller-reported state, if available
- `capabilities[]`

### `lovense_set_position`

Input:
- `toy_id`: required target identifier
- `position`: integer 0–100

Maps to Lovense's documented absolute `Position` operation where supported. Do not reinterpret this as speed.

### `lovense_play_pattern`

Input:
- `toy_id`: required target identifier
- `actions[]`: ordered objects containing:
  - `ts`: timestamp in milliseconds
  - `pos`: position 0–100

For an implementation using PatternV2, the server should validate increasing timestamps, valid positions, device support, and the documented maximum duration. It should handle the required PatternV2 initialization/setup/play protocol internally.

### `lovense_function` (optional)

Input:
- `toy_id`
- `function`: a supported function name
- `parameters`: operation-specific parameters

Expose only functions confirmed by device capability discovery. Do not assume stroke/thrust support for Solace Pro.

### `lovense_stop`

Input:
- `toy_id`: required target identifier

Stops the selected device using the appropriate supported API operation. It should not be invoked automatically due to turn completion, elapsed time, inactivity, or topic changes.

## Controller implementation requirements

- Discover toys and capabilities; do not assume a device ID or model.
- Target the selected toy rather than broadcasting to all devices.
- Validate every parameter and return structured success/error results.
- Keep API credentials, pairing data, and local connection details inside the controller's configuration or secret storage.
- Report connection state separately from command acceptance.
- Do not claim physical outcomes; report only what the API/controller can verify.
- Do not add arbitrary usage restrictions, duration caps, idle timeouts, watchdog stops, or automatic end-of-turn stops.
- Preserve a reliable explicit stop operation.

## Lovense API implementation notes

For a local Standard API implementation, the controller should follow the current official Lovense documentation for endpoint discovery, authentication, headers, and command payloads. The documented local PC Remote endpoint has used `127-0-0-1.lovense.club` on port `30010`; do not hard-code this as universal because connection details can differ by Lovense app/platform and version.

The official command API has documented:
- `Position` for absolute position control (0–100);
- `PatternV2` for pattern playback, with action timestamps in milliseconds and positions 0–100;
- generic `Function` operations only where the target toy supports them.

The controller must discover the actual host, port, pairing details, and supported operations at runtime or from the current official setup flow. Treat API response codes and error messages according to the current official documentation; do not rely on stale hard-coded assumptions.
