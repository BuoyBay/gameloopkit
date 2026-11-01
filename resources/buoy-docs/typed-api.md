---
title: "gameloopkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["FixedGameLoop"]
dependencies: ["physicskit", "spritekit"]
symbols: ["FixedGameLoop"]
related: ["api.md", "getting-started.md", "index.md"]
---

# gameloopkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## FixedGameLoop

`Public Class FixedGameLoop`

A caller-clocked bounded accumulator over an existing PhysicsWorld.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Paused` | `Boolean` | No | No | `False` | — |
| `FixedDelta` | `Double` | Yes | No | — | — |
| `PendingSeconds` | `Double` | Yes | No | — | — |
| `Alpha` | `Double` | Yes | No | — | — |
| `DroppedSeconds` | `Double` | Yes | No | — | — |
| `TotalSteps` | `Integer` | Yes | No | — | — |
| `LastSteps` | `Integer` | Yes | No | — | — |
| `BindingCount` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(world As PhysicsWorld, maxFrameSeconds As Double = 0.25, maxSteps As Integer = 8)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `world` | `PhysicsWorld` | `ByVal` | Required |
| `maxFrameSeconds` | `Double` | `ByVal` | `0.25` |
| `maxSteps` | `Integer` | `ByVal` | `8` |

Returns: no value.

### Bind

`Public Sub Bind(body As PhysicsCircle, actor As SpriteActor)`

One body and actor may occur at most once in this loop.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `body` | `PhysicsCircle` | `ByVal` | Required |
| `actor` | `SpriteActor` | `ByVal` | Required |

Returns: no value.

### Unbind

`Public Function Unbind(actor As SpriteActor) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `actor` | `SpriteActor` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### ClearBindings

`Public Sub ClearBindings()`

No parameters.

Returns: no value.

### Reset

`Public Sub Reset()`

Clear clock statistics/history without resetting physics or Paused.

No parameters.

Returns: no value.

### Advance

`Public Function Advance(elapsedSeconds As Double) As Integer`

Paused input is ignored; caller must keep sampling the clock while paused.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `elapsedSeconds` | `Double` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

### SyncSprites

`Public Sub SyncSprites(interpolate As Boolean = True)`

Place sprite centers at interpolated or current physics positions.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `interpolate` | `Boolean` | `ByVal` | `True` |

Returns: no value.
