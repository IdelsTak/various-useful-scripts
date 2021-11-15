# My PulseAudio Volume Control Keyboard Commands

| Action          | Command                                        | Shortcut               |
| --------------- | ---------------------------------------------- | ---------------------- |
| Increase volume | `pactl -- set-sink-volume @DEFAULT_SINK@ +5%`  | Ctrl + Alt + Page Up   |
| Decrease volume | `pactl -- set-sink-volume @DEFAULT_SINK@ -5%`  | Ctrl + Alt + Page Down |
| (Un)Mute        | `pactl -- set-sink-mute @DEFAULT_SINK@ toggle` | Ctrl + Alt + End       |

