# Apps - Vim 

## Vim configuration
- global clipboard:
  - check support with: `vim --version | grep clipboard`
  - if --: sudo apt install vim-gtk
  - then: create ~/.vimrc -> add: `set clipboard=unnamedplus`

## Neovim configuration
- Config file: `init.lua` = `$MYVIMRC`
- Location windows: `C:\Users\<you>\AppData\Local\nvim\init.lua`
- Location linux: `.config/nvim`
- Leader key: **Space**
- Save config: `:w` then `:source %`

## ⌨️ Common Keybindings
| Action                     | Command                                |
| -------------------------- | -------------------------------------- |
| Scroll half-page up/down   | `<C-u>` / `<C-d>`                      |
| Scroll one page up/down    | `<C-b>` / `<C-f>`                      |
| Scroll one line up/down    | `<C-y>` / `<C-e>`                      |
| Move to top/bottom of file | `gg` / `G`                             |
| Move to start/end of line  | `0` / `$`                              |
| Move word forward/backward | `w` / `b`                              |
| Move to matching bracket   | `%`                                    |
| Undo/Redo                  | `u` / `<C-r>`                          |
| Search forward/backward    | `/pattern` / `?pattern`                |
| Next/previous search match | `n` / `N`                              |
| Cancel search highlighting | `:noh`                                 |
| Replace text               | `:%s/old/new/g`                        |
| Replace in selection       | `:s/old/new/g`                         |
| Replace with confirmation  | `:%s/old/new/gc`                       |

## 🔍 Code Navigation & References
| Action                      | Command                                |
| --------------------------- | -------------------------------------- |
| Go to definition            | `gd`                                   |
| Go to declaration           | `gD`                                   |

## 🗂️ File & Session Management
| Action           | Command                         |
| ---------------- | ------------------------------- |
| Save file        | `:w`                            |
| Save all buffers | `:wa`                           |
| Quit file        | `:q`                            |
| Save + quit      | `:wq`                           |

## 🧭 Navigation
| Action                     | Command                                |
| -------------------------- | -------------------------------------- |
| Move left/right/up/down    | `h` / `l` / `k` / `j`                  |
| Scroll half-page           | `<C-d>` / `<C-u>`                      |
| Jump top/bottom            | `gg` / `G`                             |
| Navigate to next word      | `w`                                    |
| Navigate to previous word  | `b`                                    |
| Go back to previous position | `<C-o>`                              |
| Go forward to next position  | `<C-i>`                              |
| Go to specific line number   | `:42` or `42G` or `42gg`                 |


## ✂️ Editing
| Action                      | Command             |
| --------------------------- | ------------------- |
| Enter insert mode           | `i`                 |
| Enter ins mod after cursor  | `a`                 |
| Select text                 | `v`                 |
| Select current word         | `viw`               |
| Select block (visual block) | `<C-q>`             |
| Copy                        | `y`                 |
| Cut                         | `d`                 |
| Paste                       | `p`                 |
| Delete without yanking      | `_d`                |
| Indent selection            | `>`                 |
| Unindent selection          | `<`                 |
| Auto-indent selection       | `=`                 |
| Move line up/down           | `:m .-2` / `:m .+1` |
| Delete to end of line       | `D`                 |
| Delete to end of line and i | `C`                 |
| Delete current char         | `x`                 |
| Undo / Redo                 | `u` / `<C-r>`       |
| Select all                  | `ggVG`              |


## 💬 Commenting
| Action                      | Command             |
| --------------------------- | ------------------- |
| Comment line                | `gcc`               |
| Comment selection           | `gc`                |
| Comment to end of line      | `gc$`               |
| Toggle comment block        | `gcgc`              |
| Add comment above/below     | `gcO` / `gco`       |
| Comment with specific type  | `:Commentary`       |


## 🔍 Search
| Action                        | Command       |
| ----------------------        | ------------- |
| Find text                     | `/text`       |
| Next / previous match         | `n` / `N`     |
| Clear search highlight        | `<Esc>`       |
| Search word under cursor      | `*` / `#`      |
| Search with special chars     | `/\[001\]`    |



## 📁 File Management
| Action                      | Command             |
| --------------------------- | ------------------- |
| Disable backup files (~)    | `:set nobackup`     |
| Disable swap files (.swp)  | `:set noswapfile`   |
| Set backup directory        | `:set backupdir=~/tmp` |
| Permanently disable backups | Add `set nobackup` to `~/.vimrc` |

> **Note:** Files ending with `~` are backup files created by Vim. This is normal behavior. Vim creates these automatically to preserve your work in case of crashes. You can disable them if desired using the commands above.
