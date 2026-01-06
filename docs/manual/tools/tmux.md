# TMUX Plugins Keybindings and Usage Notes

This note outlines keybindings and usage for a selection of TMUX plugins, assuming `C-a` (Control+a) is set as the prefix key. Adjust these keybindings according to your TMUX configuration.

## General Plugins

### TPM (Tmux Plugin Manager)
- **Reload TMUX environment**: `C-a` + `I`
  - Reloads TMUX environment and installs any new plugins specified in `.tmux.conf`.
- **Update installed plugins**: `C-a` + `U`
  - Updates all installed plugins to their latest versions.
- **Remove/uninstall plugins not on the plugin list**: `C-a` + `alt+u`
  - Cleans up plugins that are no longer listed in your `.tmux.conf`.

### tmux-resurrect
- **Save TMUX environment**: `C-a` + `Ctrl+s`
  - Saves the current TMUX session, windows, panes, and their layouts.
- **Restore TMUX environment**: `C-a` + `Ctrl+r`
  - Restores the previously saved TMUX session.

### tmux-sensible
- Provides sensible default settings for TMUX. No specific keybindings, it enhances overall user experience automatically.

## Navigation & Utilities

### vim-tmux-navigator
- **Seamless navigation between TMUX panes and vim splits**:
  - Navigate between vim and TMUX panes using `C-h`, `C-j`, `C-k`, `C-l` without needing to prefix with `C-a`.

### tmux-open
- **Open files and URLs from TMUX panes**:
  - Open file/URL under cursor: `C-a` + `o`
  - This command attempts to open the item in the most appropriate application.

### tmux-yank
- **Yank (copy) text to the clipboard**:
  - Copy mode: `C-a` + `[`, then navigate to text, start selection with `Space`, move, and yank with `Enter`.
  - Yanked text is copied to the system clipboard.

### tmux-fzf-url
- **Open URLs using fzf**:
  - Open URL from pane: `C-a` + `u`
  - Lists URLs in the current pane using fzf, allowing you to select one to open in your default browser.

Next: [Guide](nvim.md)
