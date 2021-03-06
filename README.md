[![Build Status](https://travis-ci.org/lotabout/skim.svg?branch=master)](https://travis-ci.org/lotabout/skim)

> Life is short, skim!

Half of our life is spent on navigation: files, lines, commands... You need
skim! It is a general fuzzy finder that saves you time.

It is blazingly fast as it reads the data source asynchronously.

![skim demo](https://cloud.githubusercontent.com/assets/1527040/21603846/09138f6e-d1db-11e6-9466-711cc5b1ead8.gif)

skim provides a single executable: `sk`, basically anywhere you would want to use
`grep` try `sk` instead.

# Installation

skim project contains several components:

1. `sk` executable -- the core.
2. `sk-tmux` -- script for launching `sk` in a tmux plane.
3. vim/nvim plugin -- to call `sk` inside vim/nvim. check [skim.vim](https://github.com/lotabout/skim.vim) for more vim support.

## Linux

Clone this repository and run the install script:

```sh
git clone --depth 1 git@github.com:lotabout/skim.git ~/.skim
~/.skim/install
```

Next: add `~/.skim/bin` to your PATH by putting the following line into your `~/.bashrc`

```
export PATH="$PATH:$HOME/.skim/bin"
```

As an alternative, you can directly [download the sk
executable](https://github.com/lotabout/skim/releases), but extra utilities are recommended.

## OSX

Using Homebrew:

```
brew install sbdchd/skim/skim
```

But the Linux way described above will also work.

## Install as vim plugin
Once you have cloned the repository, add the following line to your .vimrc.

```
set rtp+=~/.skim
```

Or you can have vim-plug manage skim (recommended):

```
Plug 'lotabout/skim', { 'dir': '~/.skim', 'do': './install' }
```

## Build Manually

Clone the repo and run:

```
cargo build --release
```

then put the resulting `target/release/sk` executable on your PATH.

# Usage

skim can be used as a general filter(like `grep`) or as an interactive
interface for invoking commands.

## As filter

Try the following

```
# directly invoke skim
sk

# or pipe some input to it: (press TAB key select multiple items with -m enabled)
vim $(find . -name "*.rs" | sk -m)
```
The above command will allow you to select files with ".rs" extension and open
the ones you selected in vim.

## As Interactive Interface

`skim` can invoke other commands dynamically. Normally you would want to
integrate it with [rg](https://github.com/BurntSushi/ripgrep)
[ag](https://github.com/ggreer/the_silver_searcher) or
[ack](https://github.com/petdance/ack2) for searching contents in a project
directory:

```
# work with ag
sk --ansi -i -c 'ag --color "{}"'
# or with rg
sk --ansi -c 'rg --color=always --line-number "{}"'
```

![interactive mode demo](https://cloud.githubusercontent.com/assets/1527040/21603930/655d859a-d1db-11e6-9fec-c25099d30a12.gif)

## Key Bindings

Some common used keybindings.

| Key               | Action                                     |
|------------------:|--------------------------------------------|
| Enter             | Accept (select current one and quit)       |
| ESC/Ctrl-G/Ctrl-Q | Abort                                      |
| Ctrl-P/Up         | Move cursor up                             |
| Ctrl-N/Down       | Move cursor Down                           |
| TAB               | Toggle selection and move down (with `-m`) |
| Shift-TAB         | Toggle selection and move up (with `-m`)   |

## Search Syntax

`skim` borrowed `fzf`'s syntax for matching items:

| Token    | Match type                 | Description                       |
|----------|----------------------------|-----------------------------------|
| `text`   | fuzzy-match                | items that match `text`           |
| `^music` | prefix-exact-match         | items that start with `music`     |
| `.mp3$`  | suffix-exact-match         | items that end with `.mp3`        |
| `'wild`  | exact-match (quoted)       | items that include `wild`         |
| `!fire`  | inverse-exact-match        | items that do not include `fire`  |
| `!.mp3$` | inverse-suffix-exact-match | items that do not end with `.mp3` |

`skim` also support the combination of tokens.

- space has the meaning of `AND`. With the term `src main`, `skim` will search
    for items that match **both** `src` and `main`.
- ` | ` means `OR` (note the spaces around `|`). With the term `.md$ |
    .markdown$`, `skim` will search for items ends with either `.md` or
    `.markdown`.
- `OR` have higher precedence. So `readme .md$ | .markdown$` is groupped into
    `readme AND (.md$ OR .markdown$)`.

In case that you want to use regular expressions, `skim` provide `regex` mode:

```
sk --regex
```

You can switch to `regex` mode dynamically by pressing `Ctrl-R` (Rotate Mode).

## exit code

| Exit Code | Meaning                           |
|-----------|-----------------------------------|
| 0         | Exit normally                     |
| 1         | No Match found                    |
| 130       | Abort by Ctrl-C/Ctrl-G/ESC/etc... |

## Customization

skim can be customized with lots of options. You can use them to create new
bash/zsh/.. functions by yourself. Use your imagination :)

## Key binding & Actions

Specify the bindings with comma seperated pairs(no space allowed), example:

`sk --bind 'alt-a:select-all,alt-d:deselect-all'`

| Action               | Default key                 |
|----------------------|-----------------------------|
| abort                | esc, ctrl-c, ctrl-g, ctrl-q |
| accept               | enter                       |
| backward-char        | left, ctrl-b                |
| backward-delete-char | ctrl-h, backspace           |
| backward-kill-word   | alt-backspace               |
| backward-word        | alt-b, shift-left           |
| beginning-of-line    | ctrl-a                      |
| cancel               | None                        |
| clear-screen         | ctrl-l                      |
| delete-char          | del                         |
| delete-charEOF       | ctrl-d                      |
| deselect-all         | None                        |
| down                 | ctrl-j, ctrl-n, down        |
| end-of-line          | ctrl-e, end                 |
| forward-char         | ctrl-f, right               |
| forward-word         | alt-f, shift-right          |
| ignore               | None                        |
| kill-line            | ctrl-k                      |
| kill-word            | alt-d                       |
| page-down            | page-down                   |
| page-up              | page-up                     |
| rotate-mode          | ctrl-r                      |
| scroll-left          | alt-h                       |
| scroll-right         | alt-l                       |
| select-all           | None                        |
| toggle               | None                        |
| toggle-all           | None                        |
| toggle-down          | tab                         |
| toggle-interactive   | ctrl-q                      |
| toggle-out           | None                        |
| toggle-sort          | None                        |
| toggle-up            | shift-tab                   |
| unix-line-discard    | ctrl-u                      |
| unix-word-rubout     | ctrl-w                      |
| up                   | ctrl-p, ctrl-k, up          |

## Sort criterion

There are four sort keys for results: `score, index, begin, end`, you can
specify how the records are sorted by `sk --tiebreak score,index,-begin` or any
other order you want.

## Color Scheme

It is a high chance that you are a better artist than me. Luckily you won't
be stuck with the default colors, `skim` supports customization of the color scheme.

```
--color=[BASE_SCHEME][,COLOR:ANSI]
```

The configuration of colors starts with the name of the base color scheme and
followed by custom color mappings. For example:


```
sk --color=current_bg:24
sk --color=light,fg:232,bg:255,current_bg:116,info:27
```

You can choose the `BASE SCHEME` among the following(default: dark on
256-color terminal, otherwise 16):


| Base Scheme | Description                               |
|-------------|-------------------------------------------|
| dark        | Color scheme for dark 256-color terminal  |
| light       | Color scheme for light 256-color terminal |
| 16          | Color scheme for 16-color terminal        |
| bw          | No colors                                 |

While the customisable `COLOR`s are

| Color            | Description                                      |
|------------------|--------------------------------------------------|
| fg               | Text                                             |
| bg               | Background                                       |
| matched          | Text color of matched items                      |
| matched_bg       | Background color of matched items                |
| current          | Text color (current line)                        |
| current_bg       | Background color (current line)                  |
| current_match    | Text color of matched items (current line)       |
| current_match_bg | Background color of matched items (current line) |
| spinner          | Streaming input indicator                        |
| info             | Info area                                        |
| prompt           | Prompt                                           |
| cursor           | Cursor                                           |
| selected         | Text color of "selected" indicator               |

## Misc

- `--ansi`: to parse ANSI color codes(e.g `\e[32mABC`) of the data source
- `--regex`: use the query as regular expression to match the data source

## Interactive mode

In interactive mode, `sk` will pass the query to the command you specified and
present the output to you. You can specify the command using the `-c` option:

`sk -i -c 'ag --color "{}"'`

In the above example, the replace string `{}` will be replaced with the query you
type before invoking the command. Use `-I <replstr>` to change replstr if you
want.

For example, with the input "hello" in interactive mode, `skim` will replace
the above command with `ag --color "hello"` and invoke it.

If you want to further narrow down the result returned by the command, press
`Ctrl-Q` to toggle interactive mode.

## Fields support

Normally only plugin users need to understand this.

For example, you have the data source with the format:

```
<filename>:<line number>:<column number>
```

However, you want to search `<filename>` only when typing in queries. That
means when you type `21`, you want to find a `<filename>` that contains `21`,
but not matching line number or column number.

You can use `sk --delimiter ':' --nth 0` to achieve this.

Also you can use `--with-nth` to re-arrange the order of fields.

**Range Syntax**

- `<num>` -- to specify the `num`-th fields, starting with 0.
- `start..` -- starting from the `start`-th fields, and the rest.
- `..end` -- starting from the `0`-th field, all the way to `end`-th field,
    excluding `end`.
- `start..end` -- starting from `start`-th field, all the way to `end`-th
    field, excluding `end`.

## Difference to fzf

[fzf](https://github.com/junegunn/fzf) is a command-line fuzzy finder written
in Go and [skim](https://github.com/lotabout/skim) tries to implement a new one
in Rust!

This project is written from scratch. Some decisions of implementation are
different from fzf. For example:

1. The fuzzy search algorithm is different.
2. UI of showing matched items. `fzf` will show only the range matched while
   `skim` will show each character matched.
3. `skim` has an interactive mode.
4. `skim`'s range syntax is git style.

## How to contribute

[Create new issues](https://github.com/lotabout/skim/issues/new) if you meet any bugs
or have any ideas. Pull requests are warmly welcomed.
