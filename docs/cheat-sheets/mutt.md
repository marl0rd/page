# Apps - Mutt Terminal Email Cheatsheet

## Launch / startup
- Start mutt (default spool/INBOX): `mutt` | e.g.: `mutt`
- Open specific mailbox: `mutt -f <mailbox>` | e.g.: `mutt -f ~/Mail/inbox`
- Open mailbox read-only: `mutt -R -f <mailbox>` | e.g.: `mutt -R -f ~/Mail/archive`
- Use alternate config file: `mutt -F <config>` | e.g.: `mutt -F ~/.muttrc.work`
- Resume postponed draft: `mutt -p` | e.g.: `mutt -p`
- Show version: `mutt -v` | e.g.: `mutt -v`

## Send mail from CLI
- Send subject + body from stdin: `mutt -s "<subject>" -- <addr>` | e.g.: `mutt -s "hi" -- me@x < body.txt`
- Add CC / BCC: `mutt -c <cc> -b <bcc> -s "<subject>" -- <to>` | e.g.: `mutt -c cc@x -b bcc@x -s "rpt" -- to@x < rpt.txt`
- Attach file(s) (must be last option): `mutt -a <file...> -- <to>` | e.g.: `mutt -a log.txt -- dev@x`

## Index (message list) navigation
- Next message: `j` | e.g.: `j`
- Previous message: `k` | e.g.: `k`
- Open message: `<Enter>` | e.g.: `<Enter>`
- Next unread message: `<Tab>` | e.g.: `<Tab>`
- Delete message: `d` | e.g.: `d`
- Undelete message: `u` | e.g.: `u`
- Tag message: `t` | e.g.: `t`
- Save message to mailbox: `s` | e.g.: `s`
- Copy message to mailbox: `C` | e.g.: `C`
- Limit messages by pattern: `l` | e.g.: `l`
- Search forward: `/` | e.g.: `/`
- Quit (apply changes): `q` | e.g.: `q`
- Quit (abort changes): `x` | e.g.: `x`

## Reading mail (pager) — stay in the same email
- Scroll down one line: `Enter` | e.g.: `Enter`
- Scroll up one line: `Backspace` | e.g.: `Backspace`
- Page down: `Space` | e.g.: `Space`
- Page up: `-` | e.g.: `-`
- Half page down: `d` | e.g.: `d`
- Half page up: `u` | e.g.: `u`
- Jump to top of message: `Home` | e.g.: `Home`
- Jump to bottom of message: `End` | e.g.: `End`
- Show pager key bindings: `?` | e.g.: `?`

## Prevent auto-jump to next email
- Stop pager from advancing at end-of-message: `set pager_stop=yes` | e.g.: add to `~/.muttrc`

***muttrc
# Do not jump to next message at end of email
set pager_stop=yes
***

## Fix arrow keys scrolling messages (pager)
- Bind arrows to scroll message content (not messages)

***muttrc
# Make arrow keys scroll within the current email
bind pager <down> next-line
bind pager <up>   previous-line
***

## Pager actions (while reading)
- Reply to sender: `r` | e.g.: `r`
- Reply to all: `g` | e.g.: `g`
- Forward message: `f` | e.g.: `f`
- View attachments: `v` | e.g.: `v`
- Delete message: `d` | e.g.: `d`
- Return to index: `q` | e.g.: `q`

## Compose mode
- Send message: `y` | e.g.: `y`
- Abort message: `q` | e.g.: `q`
- Postpone draft: `P` | e.g.: `P`
- Edit To field: `T` | e.g.: `T`
- Edit Cc field: `c` | e.g.: `c`
- Edit Bcc field: `b` | e.g.: `b`
- Edit Subject: `s` | e.g.: `s`
- Attach file: `a` | e.g.: `a`
- Set Fcc (sent folder): `f` | e.g.: `f`
