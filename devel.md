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

# GitHub CLI

Using the [GitHub CLI](https://cli.github.com/) to manage repositories from command line. 

## Listing Repositories

Listing repoisitories with GH cli:

```
mgarcia@PC-KL-26743:~/Work/clamdock$ gh repo list

Showing 27 of 27 repositories in @marcelomgarcia

NAME                     DESCRIPTION               INFO          UPDATED
marcelomgarcia/mgnotes   Marcelo's notes  public     about 16 days ago
```

Listing repositories for other "organizations:"

```
mgarcia@PC-KL-26743:~/Work/clamdock$ gh repo list kaust-library

Showing 22 of 22 repositories in @kaust-library

NAME   DESCRIPTION                          INFO          UPDATED
kaust-library/clamdock   Running ClamAV inside Docker (...)
```

## Creating a Repository

Creating a repository for Python development with `.gitignore` and MIT license.

```
mgarcia@PC-KL-26743:~/Work/clamdock$ gh repo create kaust-library-systems/IRTSv2 \
--public \
--add-readme \
--description "Updated version of IRTS after refactoring with Claude" \
--license MIT \
--gitignore Python
✓ Created repository kaust-library-systems/IRTSv2 on github.com
  https://github.com/kaust-library-systems/IRTSv2
mgarcia@PC-KL-26743:~/Work/clamdock$
```

# UV

## Creating the Virtual Environment

I prefer to have the virtual environment hidden, so to create the a `.venv` directory:

```
mgarcia@PC-KL-26743:~/Work/pyIRTS$ uv venv .venv
Using CPython 3.13.6
Creating virtual environment at: .venv
Activate with: source .venv/bin/activate
mgarcia@PC-KL-26743:~/Work/pyIRTS$
```

## Requiments

To add requirements to the virtual environment

```
mgarcia@PC-KL-26743:~/Work/pyIRTS$ uv pip install -r requirements.txt
Resolved 17 packages in 3.26s
Prepared 6 packages in 3.08s
Installed 17 packages in 40ms
 + certifi==2025.10.5
 + charset-normalizer==3.4.4
 (...)
```
