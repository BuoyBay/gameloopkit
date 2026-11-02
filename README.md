# Drive fixed-step physics and interpolated sprite updates

Caller-clocked fixed-step accumulation around PhysicsKit, with interpolated
SpriteKit placement. `Import gameloopkit`; consumers also import `physicskit`
and `spritekit` when using their types directly. There are no timers, threads,
wall-clock reads or window creation inside the package.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add gameloopkit --git https://github.com/BuoyBay/gameloopkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.
BPM also resolves `physicskit`, `spritekit`.

## API at a glance

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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer gameloopkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
