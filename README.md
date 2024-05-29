# wezterm-move.nvim


Navigate between `neovim` and [wezterm](https://wezterm.com/) panes.

It's just like [smart-split.nvim](https://github.com/mrjones2014/smart-splits.nvim) but:
 - only for wezterm.
 - more lazy load.
 - more minimal.

## Features
- **Navigate**: Move from neovim to wezterm panes with ease.
- **Minimal**: No dependencies, *30* lines of code.
- **Lazy load**: Fully lazy loaded with *lazy.nvim*.

## Installation

* With **lazy.nvim**

```lua
{
    "letieu/wezterm-move.nvim",
    keys = { -- Lazy loading, don't need call setup() function
      { "<C-h>", function() require("wezterm-move").move "h" end },
      { "<C-j>", function() require("wezterm-move").move "j" end },
      { "<C-k>", function() require("wezterm-move").move "k" end },
      { "<C-l>", function() require("wezterm-move").move "l" end },
    },
}
```

## Configuration Wezterm

```lua
local wezterm = require("wezterm")

local function is_vim(pane)
  local process_info = pane:get_foreground_process_info()
  local process_name = process_info and process_info.name

  return process_name == "nvim" or process_name == "vim"
end

local direction_keys = {
  Left = "h",
  Down = "j",
  Up = "k",
  Right = "l",
  -- reverse lookup
  h = "Left",
  j = "Down",
  k = "Up",
  l = "Right",
}

local function split_nav(resize_or_move, key)
  return {
    key = key,
    mods = resize_or_move == "resize" and "META" or "CTRL",
    action = wezterm.action_callback(function(win, pane)
      if is_vim(pane) then
        -- pass the keys through to vim/nvim
        win:perform_action({
          SendKey = { key = key, mods = resize_or_move == "resize" and "META" or "CTRL" },
        }, pane)
      else
        if resize_or_move == "resize" then
          win:perform_action({ AdjustPaneSize = { direction_keys[key], 3 } }, pane)
        else
          win:perform_action({ ActivatePaneDirection = direction_keys[key] }, pane)
        end
      end
    end),
  }
end

local nav_keys = {
  -- move between split panes
  split_nav("move", "h"),
  split_nav("move", "j"),
  split_nav("move", "k"),
  split_nav("move", "l"),
  -- resize panes
  split_nav("resize", "h"),
  split_nav("resize", "j"),
  split_nav("resize", "k"),
  split_nav("resize", "l"),
}

-- wezterm config
local config = {}

config.keys = nav_keys
```

## Usage

- **Navigate**: Use `Ctrl + h/j/k/l` to move between panes.
- **Resize**: Use `META + h/j/k/l` to resize panes. (`META` is `Alt` key on Windows and `Option` key on macOS)

**Note**: Resize only for wezterm panes, not for neovim panes.
If you want to resize neovim panes, you need write your own keybindings in neovim.

## Inspiration and Thanks
- **[smart-split.nvim](https://github.com/mrjones2014/smart-splits.nvim)** by @mrjones2014
