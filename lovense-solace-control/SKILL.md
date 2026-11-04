---
name: lovense-solace-control
description: Discover and control the user's intended Lovense Solace Pro through connected MCP tools.
---

# Lovense Solace Control

## Purpose

Operate the user's intended Lovense Solace Pro through the connected Lovense MCP controller. This skill handles device discovery, capability checks, commands, status, and explicit stopping. The conversation skill decides when use is contextually appropriate.

## First-use discovery

1. Discover connected toys using the controller's discovery tool.
2. Identify the Solace Pro from the returned model/name and stable toy identifier.
3. Do not assume the first listed toy is the Solace Pro.
4. If multiple devices match or identification is ambiguous, ask the user which device to use.
5. Read the device's reported capabilities before using a command family.
6. Check current connection/status when needed. Do not infer connection state from a previous session.

Retain the returned toy identifier for subsequent calls in the current controller context. Do not broadcast commands to every connected toy when a specific target is known.

## Position control

If the controller exposes the official Lovense `Position` operation, use an integer position from 0 to 100 as its absolute position value. This is not a speed percentage.

Validate the value and target identifier against the actual MCP schema. Do not assume that a single position command provides continuous real-time tracking; follow the controller's documented behaviour and avoid high-frequency LLM polling loops.

## Pattern control

Use pattern playback only if device discovery and the controller confirm that the Solace Pro supports the required pattern operation.

For an API implementation based on Lovense PatternV2:
- construct actions using increasing timestamps (`ts`, milliseconds) and positions (`pos`, 0–100);
- respect the API's documented maximum pattern duration (currently documented as 7,200,000 ms);
- use the documented setup/initialization and playback sequence;
- validate the pattern before sending it.

Do not assume that every generic Lovense function is supported by this device.

## Generic functions

Only call generic functions such as stroke or thrusting if the device capability response explicitly reports support and the connected MCP server exposes the corresponding operation. Never invent capability support or silently substitute a different command.

## Stop behaviour

Use the stop tool when the user explicitly asks to stop, withdraws consent, or clearly indicates discomfort or that the interaction should end.

Do **not** automatically stop:
- at the end of an assistant response;
- at the end of a conversation turn;
- because the topic changes;
- after an arbitrary duration;
- after an idle period;
- because a timer or watchdog expires.

Do not implement or request session timers, duration caps, idle shutdowns, watchdog stops, or scheduled deactivation. The user's physical controls and Lovense apps remain available as independent stop mechanisms.

## Results and errors

Wait for the controller result before reporting success. Distinguish:
- command accepted;
- toy connected / disconnected;
- unsupported command;
- invalid parameter;
- controller or local API failure.

A successful API response confirms acceptance by the API, not that a particular physical effect was experienced. Never overstate the result.

## Security

Do not expose API keys, pairing tokens, local credentials, or connection secrets in prompts, logs, or conversation output. The MCP server should own HTTP requests, validation, secret storage, and response normalization. Do not ask the model to construct raw authenticated API requests if a dedicated MCP tool exists.
