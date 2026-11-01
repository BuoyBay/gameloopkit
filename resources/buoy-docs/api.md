---
title: "gameloopkit API contracts"
summary: "Usage and behavioral contracts for gameloopkit."
category: "Reference"
symbols: ["FixedGameLoop"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# GameLoopKit 0.1.0

Caller-clocked fixed-step accumulation around PhysicsKit, with interpolated
SpriteKit placement. `Import gameloopkit`; consumers also import `physicskit`
and `spritekit` when using their types directly. There are no timers, threads,
wall-clock reads or window creation inside the package.

## API

- `FixedGameLoop(world, maxFrameSeconds = 0.25, maxSteps = 8)` retains the world.
  The frame cap must be at least `world.FixedDelta`, at most 60 seconds; the
  step cap is `1..1000`.
- `Advance(elapsedSeconds)` accepts finite `0..3600`, returns the number of
  completed steps and updates `LastSteps`, `TotalSteps`, `PendingSeconds`,
  `Alpha` and `DroppedSeconds`. It first caps frame time, then performs at most
  the step cap. Excess complete steps are dropped, retaining fractional time.
  This deliberately slows the simulation during overload instead of scheduling
  unbounded catch-up. No epsilon is added to the clock: Double rounding at a
  boundary can postpone a step until later input.
- `Paused` makes Advance ignore valid input and report zero LastSteps, retaining
  phase and interpolation history. Paused time is not counted as dropped. A
  caller sampling a real clock must continue updating its timestamp while paused.
- `Bind(body, actor)` retains a center-position binding; each body and actor may
  appear once, up to 256 bindings. The body must belong to the supplied world:
  PhysicsWorld does not expose membership introspection, so this is a caller
  precondition. Bind does not add it or add the actor to a scene. `Unbind(actor)`
  reports removal; `ClearBindings()` releases all bindings.
- `SyncSprites(interpolate = True)` centers actors using previous/current body
  positions and Alpha. Interpolation is conventionally one physics step behind
  the current world. Pass False for current positions. Every sprite position is
  checked before any actor moves. Actor depth, size, tint and animation are left
  alone. Do not independently move bound actors between synchronization and draw.
- `Reset()` clears clock statistics/pending time and captures current body
  positions as both interpolation endpoints. It does not reset the world or
  Paused. Use it after teleporting bodies or otherwise changing physics externally.

Each completed physics step is committed independently. If a later Step throws,
earlier steps and their history remain committed; the failed step's pending time
is retained. Fix the physics and retry Advance(0), or Reset. Interpolated sync
rejects pending time of one or more full steps after failure. Current-position
sync remains available. Invalid elapsed input rejects before clock mutation.
Statistics are bounded to 1e12 steps/dropped seconds; Reset permits reuse.

All access belongs on one thread. Bindings retain objects strongly. One loop
should own stepping a world; multiple active loops over the same world do not
coordinate. PhysicsKit's discrete collision and SpriteKit's rendering limits
still apply. This does not add collision callbacks, spatial indexing, animation
scheduling, clock smoothing, input queues or a native GPU backend.

## Verification and example


The runner seals all three source packages into an isolated project, checks
module/test/demo across six desktop targets, executes deterministic clock and
synchronization tests, and builds the Canvas demo. Linux execution uses the host's
existing JPEG compatibility library when its UI runtime needs it. `BUOY` selects
the compiler and `BPM_PACKAGER` the packager (default `/usr/bin/buoy`). Other
platforms are compiler-checked. The native Linux GUI regression described below
separately verifies automated interaction.

The example combines
bouncing physics bodies and interpolated sprites. Click to pause/resume; S
stops/restarts the timer with a fresh elapsed-time baseline. Its
Timer samples a monotonic clock, passes elapsed time to the loop, synchronizes
sprites, then invalidates the Canvas; drawing itself does not advance physics.

Maintainers run the private validation suite before release. See the repository README for public build instructions.

The verifier rejects unexpected stdout/stderr and post-finish output, including
with Python optimization enabled. It stages full artifact generations and leaves
the previous successful evidence unchanged if a run fails. After resealing final
documentation, --update-upstream-evidence refreshes the default report plus the
two local supporting JSON records without changing issue status or publishing.

The BPM manifest references sibling PhysicsKit/SpriteKit source packages for
development. Distribute their sealed archives alongside GameLoopKit's archive.
Nothing is published to a registry.


[Typed API](typed-api.md) · [Getting started](getting-started.md)
