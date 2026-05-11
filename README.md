# pyenv helper

This helper automatically activates/deactivates `pyenv` based virtualenvs when changing directories.
It aliases `cd` to add the functionality.
To install, simply append the code to your `.bashrc`, re-source it or log-out and -in again.

The function looks for the files `.python-version` and `.python-virtualenv` in the current directory and, recursively, up the tree, until it reaches either the user's home or `/`.
If it doesn't find the files, it deactivates an active `pyenv` virtualenv, if it finds them, it tries to activate this particular environment.

You can install `pyenv` with `curl https://pyenv.run | bash` and follow the instructions in the terminal to set it up.

#### .python-virtualenv

This file only contains the name of the virtualenv.

#### .python-version

This file contains the python version you defined when you created the virtualenv.

## Script

```bash
# cd
function cd() {
  builtin cd "$@"
  
  CURR_DIR="$PWD"
  FOUND_ENV=0
  CURRENT_ENV=''
  HAS_ACTIVE_ENV=0
  
  if [[ ! -z "$VIRTUAL_ENV" ]]; then
    CURRENT_ENV=$(basename "$VIRTUAL_ENV")
    HAS_ACTIVE_ENV=1
  fi

  if [[ -z "$CURRENT_ENV" ]]; then
    if [[ -f "$CURR_DIR/.python-virtualenv" ]] ; then
      TARGET_ENV=$(cat "$CURR_DIR/.python-virtualenv")
      if [[ "$HAS_ACTIVE_ENV" -eq 1 ]]; then
        if [[ "$CURRENT_ENV" != "$TARGET_ENV" ]]; then
          source deactivate > /dev/null 2>&1
          source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
        fi
      else
        source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
      fi
    else
      while [[ "$CURR_DIR" != "$HOME" && $FOUND_ENV -eq 0 ]]; do
        CURR_DIR=$(dirname "$CURR_DIR")
        if [[ -f "$CURR_DIR/.python-virtualenv" ]] ; then
          TARGET_ENV=$(cat "$CURR_DIR/.python-virtualenv")
          if [[ "$HAS_ACTIVE_ENV" -eq 1 ]]; then
            if [[ "$CURRENT_ENV" != "$TARGET_ENV" ]]; then
              source deactivate > /dev/null 2>&1
              source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
            fi
          else
            source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
          fi
          break
        fi
      done
      if [[ "$CURR_DIR" == "$HOME" && ! -f "$CURR_DIR/.python-virtualenv" ]]; then
        source deactivate > /dev/null 2>&1
      fi
    fi
  else
    if [[ -f "$CURR_DIR/.python-virtualenv" ]]; then
      TARGET_ENV=$(cat "$CURR_DIR/.python-virtualenv")
      if [[ "$HAS_ACTIVE_ENV" -eq 1 ]]; then
        if [[ "$CURRENT_ENV" != "$TARGET_ENV" ]]; then
          source deactivate > /dev/null 2>&1
          source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
        fi
      else
        source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
      fi
    else
      while [[ "$CURR_DIR" != "$HOME" && $FOUND_ENV -eq 0 ]]; do
        CURR_DIR=$(dirname "$CURR_DIR")
        if [[ -f "$CURR_DIR/.python-virtualenv" ]] ; then
          TARGET_ENV=$(cat "$CURR_DIR/.python-virtualenv")
          if [[ "$HAS_ACTIVE_ENV" -eq 1 ]]; then
            if [[ "$CURRENT_ENV" != "$TARGET_ENV" ]]; then
              source deactivate > /dev/null 2>&1
              source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
            fi
          else
            source "$HOME/.pyenv/versions/$TARGET_ENV/bin/activate"
          fi
          break
        fi
      done
      if [[ "$CURR_DIR" == "$HOME" && ! -f "$CURR_DIR/.python-virtualenv" ]]; then
        source deactivate > /dev/null 2>&1
      fi
    fi
  fi
}
```
