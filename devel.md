Development
===========

# Git Config

[Initial config](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)

```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
# To be politically correct and use "main" instead of "master" 
$ git config --global init.defaultBranch main
```

## Auto CRLR

This option sets Git to handle the simple but very annoying problem of Windows and MacOS/Linux [end of line character](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration).

If on Windows, set it to `true` to convert `LF` to `CRLF` automatically.

```
git config --global core.autocrlf true
```

On MacOS/Linux, set it to `input` to tell Git to convert CRLF to LF on commit but not the other way around.

```
git config --global core.autocrlf input
```

Windows **only**, with no MacOS/Linux collaborators, set it to `false`

```
git config --global core.autocrlf false
```
