# IsuDeck OBS Studio Plugin

IsuDeck's OBS Studio plugin connects to the OBS WebSocket v5 server and exposes scene, audio, streaming, recording, virtual camera, replay buffer, Studio Mode, source visibility, and screenshot actions.

## Requirements

- OBS Studio 28 or later with the obs-websocket server enabled (OBS 28+ includes obs-websocket v5).
- IsuDeck with the OBS Studio plugin installed.
- Rust stable for building the plugin from source.

## Configure OBS

1. In OBS, open **Tools → WebSocket Server Settings**.
2. Enable the WebSocket server and note its port (default `4455`).
3. Keep authentication enabled and set a strong password.
4. In IsuDeck, open **Plugins → OBS Studio → Configure** and set the host, port, and password.
5. Keep the WebSocket server bound to localhost unless remote access is specifically needed. Do not expose it directly to the public internet.

The password is used to authenticate the OBS WebSocket v5 connection. IsuDeck stores plugin configuration locally; do not share configuration files containing credentials.

## Actions

| Action | Description |
|---|---|
| `change_scene` | Change the program scene. |
| `set_preview_scene` | Set the preview scene in Studio Mode. |
| `toggle_mute`, `set_mute` | Toggle or set an audio input's mute state. |
| `set_input_volume` | Set an audio input to 0–100 percent. |
| `toggle_record`, `start_record`, `stop_record`, `toggle_record_pause` | Control local recording. |
| `toggle_stream`, `start_stream`, `stop_stream` | Control streaming. |
| `toggle_virtual_cam`, `start_virtual_cam`, `stop_virtual_cam` | Control the OBS Virtual Camera. |
| `toggle_replay_buffer`, `start_replay_buffer`, `stop_replay_buffer`, `save_replay_buffer` | Control and save the replay buffer. |
| `toggle_studio_mode`, `trigger_studio_transition` | Control Studio Mode and trigger its transition. |
| `toggle_source_visibility`, `set_source_visibility` | Toggle or set a scene item's visibility. |
| `take_screenshot` | Save a program screenshot to the user's `Pictures` folder. |

Scene and input selectors are populated from OBS. Actions with live OBS state also publish IsuDeck `state_update` messages for button badges.

## Build and test

From the repository root:

```powershell
cargo fmt --manifest-path plugins-source/obs_plugin/Cargo.toml -- --check
cargo test --manifest-path plugins-source/obs_plugin/Cargo.toml --locked
cargo clippy --manifest-path plugins-source/obs_plugin/Cargo.toml --all-targets -- -D warnings
cargo build --manifest-path plugins-source/obs_plugin/Cargo.toml --release
```

The release executable is `plugins-source/obs_plugin/target/release/obs_plugin.exe` on Windows. To package it with IsuDeck, place the executable in `plugins/IsuDeck.OBSPlugin/` using the name declared by that plugin's `plugin.json`.

## Plugin protocol

The executable is a long-running IsuDeck daemon. It reads one JSON message per line from stdin and writes one JSON response per line to stdout. Diagnostic messages go to stderr so they do not corrupt the protocol stream.

The plugin consumes `init` (configuration), `get_data` (dynamic scene/input selectors), and `execute` (actions) messages. OBS WebSocket traffic uses the v5 hello/identify/request/event flow, including challenge-response authentication.

## Contributing

Bug reports and pull requests are welcome. Before submitting a change, run the formatting, test, Clippy, and release-build commands above. Please include a test for new parsing or protocol behavior.

## License

Licensed under GNU General Public License v3.0 only. See [`LICENSE`](./LICENSE).
