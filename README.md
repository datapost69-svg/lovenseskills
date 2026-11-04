# Lovense Skills for Odysseus

A pair of Odysseus skills for conversationally driven Lovense Solace Pro control.

## Contents

- `lovense-conversation/SKILL.md` — guidance for deciding when device interaction is contextually appropriate during consensual adult conversation.
- `lovense-solace-control/SKILL.md` — guidance for discovering and controlling the intended Lovense device through MCP.
- `lovense-solace-control/references/mcp-tool-contract.md` — a proposed MCP tool contract for the controller implementation.

## Important: skills are not the controller

These files provide instructions to an Odysseus agent. They do **not** implement Lovense connectivity or create MCP tools. A compatible Lovense MCP server/controller must be installed, connected to Odysseus, and expose tools matching the contract (or the skill instructions must be adapted to the actual tool names and schemas).

The tool names in the reference contract are proposed names, not claims that those tools already exist.

## Design goals

- Use the conversation's meaning and ongoing context, not fixed trigger words or command phrases.
- Allow the agent to initiate an appropriate device action without waiting for an explicit device command.
- Let the agent naturally acknowledge device use when it is relevant.
- Discover the actual connected toy and its supported capabilities rather than assuming them.
- Do not impose arbitrary session timers, duration limits, idle shutdowns, watchdog stops, or automatic stop-at-end-of-turn behaviour.
- Keep the user in control. Explicit stop requests must be honoured, and the physical controls / Lovense apps remain available.

## Setup

1. Connect a Lovense controller MCP server to Odysseus.
2. Confirm which tools it actually exposes and how it identifies the Solace Pro.
3. Add both skill folders to Odysseus using its Skills interface.
4. Adapt the proposed tool names and argument schemas in the control skill if the connected MCP server differs.
5. Test discovery, status reporting, a low-intensity position command, pattern playback (if supported), and explicit stop before relying on autonomous use.

The controller should report command acceptance, device connectivity, and errors accurately. A successful API response means the command was accepted; it does not prove the user's physical experience.
