Work through the [clojure-koans](https://github.com/functional-koans/clojure-koans)

Close Window - when you have 2 windows open put your cursor in the window you want to stay in and do `C-x 1`

`M-x` kill-some-buffers is a good way to go through files you've opened and close them

[This](http://clojure-doc.org/articles/tutorials/emacs.html) is very helpful with basic clojure commands, seemed like at first none of them were working (possibly related to evil-mode)

These are the [cider](https://github.com/clojure-emacs/cider#cider-mode) commands, you need to have the repl running for them to work
| Command | Description |
------------|-------------
| `C-c C-k` | compile the current file (buffer). This will also make it available  in the repl, but you have to switch the repl namespace |
| `C-c ,` | run tests in current file|
| `C-c M-n` | switch the repl to the namespace of the current file|
`C-x C-e` | will send the current s-expr to the buffer, be careful with evil | mode though because you need to be after the ) in insert mode|
`C-c C-d` | will open a window with the documentation of the function under the | cursor|
| `M-.` | will show you the source code for the current function|
| `M-,` | will pop you out like a stack after using `M-.`|
| `C-x k enter` | will kill your current buffer|
| `C-h k *keybinding*` | describes the function bound to keybinding|
| `M-x` describe-function | helpful for finding keybinding for a function|
| `M-x` describe-prefix-bindings | will list all the keybindings and what they are bound to |
| `M-(` | Wrap thing under point with  ( ) "missing [ ] around arguments of a function definition
    ; press [ to add [] then inside [] use M-) to pull in args one by one"
|`C-)` | Will move the next ) to encompass the next expression, should be able to use C-rightarrow but I think OSX hijacks that |
| `C-(` | Will do likewise with the previous expression |
| `C-x b` | You can switch buffers *and* create new ones |
| `C-M-f and C-M-b` | jump you to end & beginning of s-expr |

---
* really good tutorial for n00b [http://www.braveclojure.com/basic-emacs/]
* Can fix the C-arrow stuff on OSX by turning off mission control shortcuts [http://superuser.com/questions/638697/in-emacs-my-c-right-and-c-left-on-my-mac-keyboard-does-not-work]
* Awesome that i can run a shell in emacs [http://www.linux-france.org/article/appli/emacs/manuel/html/shell.html] I think I almost like this better than vim / tmux
