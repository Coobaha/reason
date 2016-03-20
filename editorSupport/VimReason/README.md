VimReason: Vim support for Reason
=========================================

Installation:
============

[VimBox](https://github.com/jordwalke/vimbox) is a great way to get started
with a modern Vim configuration. For example, the plugins it uses will enable
autocompletion when hitting the "." key after module names etc.

Installing
------------------

Currently, only works with bleeding edge merlin master branch on github.

0. Make sure bleeding edge merlin `master` branch, and `Reason` installed via `OPAM`.
  ```sh
  opam switch 4.02.1
  opam pin add -y merlin https://github.com/the-lambda-church/merlin.git
  opam pin add -y reasonsyntax git@github.com:facebook/ReasonSyntax.git
  opam pin add -y reason git@github.com:facebook/Reason.git



1. [`NeoBundle`](https://github.com/Shougo/neobundle.vim) is the best way to
   install plugins for Vim (either from github or disk). `VimBox` includes
   `NeoBundle`, but you can use `NeoBundle` by itself to install `VimReason`.

  If using `NeoBundle` place this at the end of your `.vimrc`.


```vim
" Bundle for VimReason
NeoBundleLocal /path/to/Reason/editorSupport/

if !empty(system('which opam'))
  " =================================== Merlin ================================
  let s:merlinPath=substitute(system('opam config var share'),'\n$','','') . "/merlin"
  execute "set rtp+=".s:merlinPath."/vim"
  execute "set rtp+=".s:merlinPath."/vimbufsync"
  let g:syntastic_reason_checkers=['merlin']
  let g:merlin_binary_flags = ["-pp", "reasonfmt_merlin"]
else
  " TODO: figure out opam for windows
endif

```

IDE Features:
=============
For all the features to work, you must add the following lines to your `.merlin` file:

```
FLG -pp reasonfmt_merlin
SUFFIX .re .rei
```

If you installed `VimBox`, autocomplete is already set up to work like a modern
IDE. Inside of a `.re` file, type `String` followed by a `.` and you will see
the autocomplete window automatically pop up (hit enter to accept). If not
using `VimBox`, Vim makes you press the bizarre key combination (`c-x c-u`) to
trigger autocomplete. As always, make sure to add the right
[Merlin](https://github.com/the-lambda-church/merlin) flags to your `.merlin`
file so that your build artifacts and source files can be found.


Syntastic Integration:
==========

If you installed `VimBox`, Syntastic is already installed and errors will be
underlined in red. If you didn't install `VimBox`, install `Syntastic` by
putting this command in the appropriate place:

```vim
NeoBundle "scrooloose/syntastic"
```

When you save any `.re`/`.rei` file, syntax errors and compile errors will be
shown in the location list window and the lines with errors will be underlined
in red.

Formatting:
===========

The command `:ReasonPrettyPrint` invokes the binary `reasonfmt` which must be
available on your `PATH`.

`VimReason`, doesn't use Vim's standard external formatting program bridge
because it disturbs your undo history and cursor position. Instead,
`VimReason`, implements its own buffer updates so that no modifications to your
buffer occur if the file is already formatted, and only lines requiring updates
are actually modified (so that undo/redo take you to the position where
formatting actually effected the file).

You can set `g:vimreason_extra_args_expr_reason` to control the arguments
passed to `reasonfmt` (such as `-print-width`). The contents of
`g:vimreason_extra_args_expr_reason` is a string that contains a `VimScript`
expression. This allows you do dynamically determine the formatting arguments
based on things like your window width.

  " Always wrap at 90 columns
  let g:vimreason_extra_args_expr_reason = '"-print-width 90"'

  " Wrap at the window width
  let g:vimreason_extra_args_expr_reason = '"-print-width " . ' .  "winwidth('.')"

  " Wrap at the window width but not if it exceeds 120 characters.
  let g:vimreason_extra_args_expr_reason = '"-print-width " . ' .  "min([120, winwidth('.')])"


Key Mappings:
=============

You can create a custom function and map it to a keybinding (in your `.vimrc`)
to quickly trigger formatting, and control how the formatting occurs. To enable
keymappings *only* for `reason` files, your vim must have been compiled with
`+localmap` (`:echo has('localmap')` should output `1` if your vim supports it).

For example, the following maps `cmd + shift + m` to reformat only when editing
a `reason` file.

  autocmd FileType reason map <buffer> <D-M> :ReasonPrettyPrint<Cr>


Merlin:
===========
If you have `merlin` installed, `VimReason` will also activate it for `reason`
files. Completions should work well, but most other things don't. `VimReason`
can't rely on `merlin` compilation, but does scan the `.merlin` file to pick
out flags and include paths so that it can compile individual files and report
compiler errors to syntastic.


Brace Completion:
============
`VimBox` already comes with `PairTools`, but if you don't have `VimBox`, install it using `Vundle`:

```vim
NeoBundle "git://github.com/MartinLafreniere/vim-PairTools.git"
```

Seriously - you should install `PairTools`. It is perhaps the best brace completion plugin for Vim.

Once you have `PairTools`, add this configuration in your `.vimrc`:

```vim
    autocmd FileType reason let g:pairtools_reason_pairclamp = 1
    autocmd FileType reason let g:pairtools_reason_tagwrench = 0
    autocmd FileType reason let g:pairtools_reason_jigsaw    = 1
    autocmd FileType reason let g:pairtools_reason_autoclose  = 1
    autocmd FileType reason let g:pairtools_reason_forcepairs = 0
    autocmd FileType reason let g:pairtools_reason_closepairs = "(:),[:],{:}" . ',":"'
    autocmd FileType reason let g:pairtools_reason_smartclose = 1
    autocmd FileType reason let g:pairtools_reason_smartcloserules = '\w,(,&,\*'
    autocmd FileType reason let g:pairtools_reason_antimagic  = 1
    autocmd FileType reason let g:pairtools_reason_antimagicfield  = "Comment,String,Special"
    autocmd FileType reason let g:pairtools_reason_pcexpander = 1
    autocmd FileType reason let g:pairtools_reason_pceraser   = 1
    autocmd FileType reason let g:pairtools_reason_tagwrenchhook = 'tagwrench#BuiltinNoHook'
    autocmd FileType reason let g:pairtools_reason_twexpander = 0
    autocmd FileType reason let g:pairtools_reason_tweraser   = 0
    autocmd FileType reason let g:pairtools_reason_apostrophe = 0
```

LICENSE
-------
Some files from VimReason are based on the Rust vim plugin and so we are including that license.