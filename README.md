# bevy_debug_lines
[![crates.io](https://img.shields.io/crates/v/bevy_prototype_debug_lines)](https://crates.io/crates/bevy_prototype_debug_lines)
[![docs.rs](https://docs.rs/bevy_prototype_debug_lines/badge.svg)](https://docs.rs/bevy_prototype_debug_lines)

A plugin providing a simple line drawing api for bevy.

![demo](https://github.com/Toqozz/bevy_debug_lines/blob/master/demo.gif)
[![demo_2](https://github.com/Toqozz/bevy_debug_lines/blob/master/demo_2.png)](https://i.imgur.com/ryu3SEe.gifv)
Click on the above demo to play it.

Master branch has no stability guarantees.

## About
This plugin uses a shader and sends individual points to the GPU, which then moves geometry to make a line.  This is quite fast with a significant number of lines, and there is no added cost to moving lines around.

## Usage
Add `bevy_prototype_debug_lines` to your `Cargo.toml`:
```toml
[dependencies]
bevy_prototype_debug_lines = "0.8"
# Or, for 3d:
# bevy_prototype_debug_lines = { version = "0.8", features = ["3d"] }
```

Add the plugin in your `App::new()` phase:
```rust
use bevy::prelude::*;
use bevy_prototype_debug_lines::*;

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_plugin(DebugLinesPlugin::default())
//      ...
        .run();
}
```

Draw a line in whatever system you have using the `DebugLines` resource:
```rust
fn some_system(
//  ...
    mut lines: ResMut<DebugLines>,
) {
    let start = Vec3::splat(-1.0);
    let end = Vec3::splat(1.0);
    let duration = 0.0;     // Duration of 0 will show the line for 1 frame.
    lines.line(start, end, duration);
}
```

Depth testing (drawing lines through geometry instead of always on top) can be changed using the
`DebugLinesPlugin::with_depth_test()` method:

```rust
use bevy::prelude::*;
use bevy_prototype_debug_lines::*;

fn main() {
    App::new()
    .add_plugins(DefaultPlugins)
    .add_plugin(DebugLinesPlugin::with_depth_test(true))
//  ...
    .run();
}
```

See [the examples](https://github.com/Toqozz/bevy_debug_lines/tree/master/examples) for more complete usage examples.

## Troubleshooting
### Lines do not show up

**Problem**: Lines do not show up on screen, even if I added the `DebugLinesPlugin` and
used `DebugLines::lines`

**Solution**: Check the dimension feature of `bevy_debug_lines`, when running your game,
there should be a log message looking like:
```
INFO bevy_prototype_debug_lines: Loaded 2d debug lines plugin.
```
Pay attention to **`Loaded 2d debug`** this should match what you are using in
your game. Is it a 3d game? If so, you should add the
`bevy_prototype_debug_lines/3d` feature flag to your `Cargo.toml`. It should
look like this:

```toml
bevy_prototype_debug_lines = { version = "0.8", features = ["3d"] }
```


## Running Examples
You can run the examples like so:
```shell
cargo run --example 3d --features="example_deps"
```

Where `3d` is one of the files in [the examples](https://github.com/Toqozz/bevy_debug_lines/tree/master/examples)

## Changes in `0.3.0`
In `0.3.0`, the `thickness` parameter has been removed.  I don't believe it provides enough value for the performance, time, or issues.
However, if you feel differently, let me know in [this](https://github.com/Toqozz/bevy_debug_lines/issues/2) issue.

This is technically a non-breaking change (i.e. your code will still compile) because `duration` was added which takes the same spot, but beware that your code still needs to be updated (probably just set old `thickness` values to `0`, if you don't care about duration stuff.).

## Changes in `0.6.0`
- Support Bevy 0.6.
- Use a `.wgsl` shader, which should improve web compatibility.
- The `DebugLines.depth_check` field has been removed in favor of `DebugLinesPlugin::with_depth_test()` -- see [depth_test](https://github.com/Toqozz/bevy_debug_lines/blob/master/examples/depth_test.rs) example.
- When adding the plugin, we now need to use `DebugLinesPlugin::default()` instead of just `DebugLinesPlugin`.
- Due to underlying changes in the bevy renderer, 3d and 2d have been split into separate features.  If you are using
`bevy_debug_lines` in a 3d game, add the `features = ["3d"]` line to your `bevy_prototype_debug_lines` dependency.  Sorry!

## Bevy Version Support

| bevy | bevy_prototype_debug_lines |
| --- | --- |
| 0.8 | 0.8 |
| 0.7 | 0.7 |
| 0.6 | 0.6 |
| 0.5 | 0.3 |
| 0.4 | 0.2.1 |

---

Please do not hesitate to let me know if you have any requests, improvements, or suggestions on how to make this crate more ergonomic or otherwise.
