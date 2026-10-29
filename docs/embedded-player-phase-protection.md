# Embedded player phase protection

The local `9001` server patch extends `9000` to validate movement beginning
inside solid collision boxes, including blocks created by RPGItems Ice.
RPGItems and WorldGuard do not need to be modified.

## Configuration

Keep `worlds.default.phaseProtection.enabled` set to `true` in
`pandaspigot.yml`. Check world-specific overrides as well. The feature is
enabled by default and uses the existing phase-protection setting.

The additional check runs only when the initial player collision box is
embedded. It checks the player's swept width and height against actual
collision shapes, allows direct or vanilla Y/X/Z movement, and rejects
increasing partial overlaps. A player enclosed by several blocks may have to
wait for those blocks to disappear if no non-deepening escape is available.

Exceptional scans are bounded to ten blocks per axis per movement packet.
Entering unloaded chunks is rejected rather than loading them for this check.
Plugin teleports, spectator noclip, sleeping players, and vehicle movement do
not use this additional check. It is a physical collision safeguard, not a
replacement for WorldGuard entry flags or an anti-cheat system.

## Manual acceptance checks

Use a staging copy with the production plugins, configuration, and arena:

1. As a non-operator without region bypass, verify WorldGuard still prevents
   ordinary placement and breaking.
2. Trigger Ice beside a protected wall. While embedded, mine and move toward
   the wall with Efficiency 10, 15, and 20. Repeat with diagonal movement,
   jumps, and a player already pressed against the wall. The server must not
   accept a position through the wall.
3. Repeat beside doors, panes, stairs, slabs, fences, and low ceilings.
4. Confirm Ice still appears and disappears normally. Check retreat from a
   partial overlap and escape from a single enclosing block toward open air.
   Moving deeper into an overlapping block is intentionally rejected.
5. Check walking, sprinting, jumping, step-up movement, PvP knockback, plugin
   teleports, respawning, vehicles, and spectator movement.
6. Repeat with typical player latency and concurrent Ice casts. Compare tick
   duration and allocation behavior with the previous build under equal load.

Unit tests cover isolated collision geometry. A successful build and those
tests do not establish live reproduction, plugin compatibility, or measured
performance. Restart the server after replacing its JAR; do not use reload.
