# Dynfloat - Dynamic Floating Window Management for Hyprland

A powerful scratchpad system for Hyprland that lets you dynamically assign and toggle multiple windows to numpad keys, creating flexible window sets that can be shown/hidden together.

## Features

- **Dynamic Window Sets**: Add or remove windows from slots on the fly
- **20 Slots Available**: Use numpad 0-9 with Super and Super+Ctrl modifiers
- **Toggle-Based Assignment**: Press assign key again to add/remove from set
- **Multi-Window Toggle**: Show/hide all windows in a set together
- **Window Group Support**: Handles Hyprland tabbed window groups
- **Smart State Management**: Remembers window positions across workspaces
- **Automatic Cleanup**: Removes closed windows from sets automatically

## How It Works

### Window Assignment (Super+Shift+Numpad)
- **First press**: Adds focused window to the slot
- **Second press**: Removes window from the slot
- Multiple windows can share the same slot

### Window Toggle (Super+Numpad)
Toggles ALL windows in the slot:
- If ANY windows are visible on current workspace → Hide ALL
- Else if ANY windows are hidden → Show ALL
- Else (all on other workspaces) → Bring ALL to current

### Example Workflow

```bash
# Assign Slack to slot 1
Focus Slack window → Super+Shift+1

# Add Terminal to same slot 
Focus Terminal → Super+Shift+1

# Now Super+1 toggles both Slack AND Terminal together!

# Remove Terminal from slot 1
Focus Terminal → Super+Shift+1 again

# Now Super+1 only toggles Slack
```

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/zackham/dynfloat.git ~/dynfloat
```

### 2. Install Scripts

```bash
# Copy scripts to your local bin
cp ~/dynfloat/scripts/* ~/.local/bin/
chmod +x ~/.local/bin/scratchpad-*

# Or symlink them
ln -s ~/dynfloat/scripts/scratchpad-* ~/.local/bin/
```

### 3. Add Keybindings

Add to your `~/.config/hypr/hyprland.conf`:

```bash
# Include the bindings file (update path)
source = ~/dynfloat/hyprland-bindings.conf
```

Or copy the bindings manually and update paths:

```bash
# Hide all scratchpad windows
bind = SUPER, Escape, exec, ~/.local/bin/scratchpad-hideall

# Slots 0-9 (Super + Numpad)
bind = SUPER SHIFT, KP_Insert, exec, ~/.local/bin/scratchpad-assign 0
bind = SUPER, KP_Insert, exec, ~/.local/bin/scratchpad-toggle 0
# ... etc for KP_End (1), KP_Down (2), etc.
```

### 4. Reload Hyprland

```bash
hyprctl reload
```

## Keybindings Reference

### Slots 0-9
| Key | Numpad Key | Assign (Add/Remove) | Toggle |
|-----|------------|-------------------|---------|
| 0 | Insert | Super+Shift+Numpad0 | Super+Numpad0 |
| 1 | End | Super+Shift+Numpad1 | Super+Numpad1 |
| 2 | Down | Super+Shift+Numpad2 | Super+Numpad2 |
| 3 | Page Down | Super+Shift+Numpad3 | Super+Numpad3 |
| 4 | Left | Super+Shift+Numpad4 | Super+Numpad4 |
| 5 | Center | Super+Shift+Numpad5 | Super+Numpad5 |
| 6 | Right | Super+Shift+Numpad6 | Super+Numpad6 |
| 7 | Home | Super+Shift+Numpad7 | Super+Numpad7 |
| 8 | Up | Super+Shift+Numpad8 | Super+Numpad8 |
| 9 | Page Up | Super+Shift+Numpad9 | Super+Numpad9 |

### Slots 10-19
Same pattern but with **Super+Ctrl** instead of just Super:
- Assign: **Super+Ctrl+Shift+Numpad**
- Toggle: **Super+Ctrl+Numpad**

### Special Commands
- **Super+Escape**: Hide all scratchpad windows

## Advanced Usage

### Organizing Window Sets

Create themed window sets for different workflows:

```bash
# Development setup on slot 1
Terminal → Super+Shift+1
VS Code → Super+Shift+1
Browser (docs) → Super+Shift+1

# Communication on slot 2
Slack → Super+Shift+2
Discord → Super+Shift+2
Email → Super+Shift+2

# Monitoring on slot 3
htop → Super+Shift+3
System Monitor → Super+Shift+3
```

### Window States

Windows can be in three states:
1. **Visible on current workspace**: Will be hidden when toggled
2. **Hidden (special:minimize)**: Will be shown when toggled
3. **On another workspace**: Will be moved to current when toggled

The toggle action affects ALL windows in the set based on priority:
- If ANY are visible on current workspace → hide all
- Otherwise, show/bring all to current workspace

## Configuration

Window assignments are stored in JSON files:
```
~/.config/hypr/scratchpads/slot_0.json
~/.config/hypr/scratchpads/slot_1.json
...
```

Each slot file contains an array of windows with their metadata:
```json
{
  "windows": [
    {
      "address": "0x...",
      "class": "Slack",
      "title": "Slack | workspace",
      "pid": 12345,
      "initialClass": "slack"
    },
    {
      "address": "0x...",
      "class": "Alacritty",
      "title": "Terminal",
      "pid": 12346,
      "initialClass": "Alacritty"
    }
  ]
}
```

## Troubleshooting

### Windows not toggling
- Check if window still exists: `hyprctl clients`
- Verify slot assignment: `cat ~/.config/hypr/scratchpads/slot_N.json`

### Keybindings not working
- Ensure scripts are executable: `chmod +x ~/.local/bin/scratchpad-*`
- Check Hyprland config: `hyprctl reload`
- Verify numlock is on if using numpad

### Window disappeared
- Windows are moved to `special:minimize` workspace when hidden
- Check with: `hyprctl clients -j | jq '.[] | select(.workspace.name == "special:minimize")'`

## Requirements

- Hyprland (Wayland compositor)
- jq (JSON processor)
- bash
- libnotify (for notifications)

## Contributing

Pull requests welcome! Some ideas for improvements:
- Save/restore window sizes and positions
- Per-slot configuration (e.g., layout preferences)
- Visual indicators for assigned windows
- Integration with waybar/other status bars

## Credits

Built for the [Hyprland](https://hyprland.org/) Wayland compositor.