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

## Populating a Repository

It's possible to create a [GitHub repository from an existing local repository](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github#adding-a-local-repository-to-github-with-github-cli)  with the command `gh repo create,` and choose to option local repository

```
garcm0b@KW20207:~/Work/repo2rdi$ gh repo create
? What would you like to do? Push an existing local repository to github.com
? Path to local repository .
? Repository name repo2rdi
? Repository owner kaust-library-systems
? Description Print tables in the database
? Visibility Public
✓ Created repository kaust-library-systems/repo2rdi on github.com
  https://github.com/kaust-library-systems/repo2rdi
? Add a remote? Yes
? What should the new remote be called? origin
✓ Added remote https://github.com/kaust-library-systems/repo2rdi.git
? Would you like to push commits from the current branch to "origin"? Yes
Enumerating objects: 25, done.
Counting objects: 100% (25/25), done.
Delta compression using up to 128 threads
Compressing objects: 100% (21/21), done.
Writing objects: 100% (25/25), 3.28 KiB | 840.00 KiB/s, done.
Total 25 (delta 7), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (7/7), done.
To https://github.com/kaust-library-systems/repo2rdi.git
 * [new branch]      HEAD -> master
branch 'master' set up to track 'origin/master'.
✓ Pushed commits to https://github.com/kaust-library-systems/repo2rdi.git
garcm0b@KW20207:~/Work/repo2rdi$
```

## Lazygit

Installing `Lazygit` on Debian 12 or Ubuntu LTS:

```
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | \grep -Po '"tag_name": *"v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit -D -t /usr/local/bin/
```

Or Download the `gz` file, extract and copy to a desired location, like `/usr/local/bin`.

# Python

## File Type Hinting

To type hing a file it seems that we use the class `typing.io`:

```python
from typing import IO

def describe_table(fout: IO[str], table: tuple) -> None:
    """Print table description in a nice way for humans"""
    (...)

def main():
    (...)
    with open(db_name, "w") as ff:
            (...)
            describe_table(ff, table_desc)
            (...)
```

# Env File in Bash

Reading a `.env` file in Bash:

```bash
export $(cat .env)
```

# UV

## Creating the Virtual Environment

I prefer to have the virtual environment visible, so to create the a `venv` directory:

```
garcm0b@KW20207:~/Work/etd_rag_pipeline$ uv venv venv --prompt etd_rag
Using CPython 3.12.3 interpreter at: /usr/bin/python3.12
Creating virtual environment at: venv
Activate with: source venv/bin/activate
garcm0b@KW20207:~/Work/etd_rag_pipeline$
```

Activating the enviroment as a simple test:

```
garcm0b@KW20207:~/Work/etd_rag_pipeline$ source venv/bin/activate
(etd_rag) garcm0b@KW20207:~/Work/etd_rag_pipeline$ deactivate
garcm0b@KW20207:~/Work/etd_rag_pipeline$
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
