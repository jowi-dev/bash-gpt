# Command line GPT utils

# Utilities provided

## `gpt`

Sends a prompt or series of prompts to the OpenAI discussions API and outputs
the response as it arrives.

```bash
gpt -s 'You are a helpful assistant' \
    -p 'Rewrite the second act of Much Ado About Nothing in modern English'
```

## `chat`

Provides a command line interface to have conversational interactions similar
to ChatGPT from the command line, with a searchable conversation history.
Simply execute `chat` and follow the menu prompts to start a conversation.

## `cmd`

Generates a command that can be directly executed in your `$SHELL`.

```bash
cmd 'list files in the current directory with time stamps'
```

If you want commands you execute with `cmd` to be added to your shell's history,
add something like this to your `bashrc`:

```bash
alias cmd="source $(which cmd)"
```

## `code`

A little wrapper around `gpt` focused on producing code without texty
explanations.

```bash
stack_class=$(code -l python -p 'stack class')
unit_test=$(code -l python -c "$stack_class" -p 'unit test for this class')
```

## `tester`

Runs your unit tests. If they fail, sends the error messages and relevant file
contents to `gpt` to come up with suggested fixes. Then it will offer to rerun
your tests to verify the fix.

## `utils`

Library of small utility functions and aliases:

  - `image <prompt> [<size>] > image.png` - generate an image
  - `re-image <source-file> <prompt> > new-image.png` - modify an image

# Beta features

There are a couple of scripts that work with the [new beta APIs](https://openai.com/blog/new-models-and-developer-products-announced-at-devday).

## `openai`

The analog to the `gpt` utility above. It provides sub-commands to interact
directly with the API.

## `chat-beta`

A much more advanced version of `chat` that works with the new API. The only
thing is lacking is the streaming protocol, so you get to wait on the run to
complete before you get your answers.

On the other hand, you also get a lot of new features:

- `\f $filepath` in your message to send the contents of a file
- `\exec cmd --arg2 --arg2` to send the output of a command
- `\begin ... \end` to send the output of a multi-line command
- `\www https://example.com` to send the contents of a web page

If the output of any of these is too large for a single message, it will be
split into multiple numbered messages.

# Dependencies

- [`curl`](https://curl.se/)
- [`jq`](https://github.com/jqlang/jq)
- [`gum`](https://github.com/charmbracelet/gum)

# Installation

## Directly

1. Ensure that `/usr/local/bin` is in your `PATH`
2. Ensure that you have write permissions to `/usr/local/bin`
3. Download to `/usr/local/bin`
```bash

for tool in gpt chat code cmd tester; do
  curl -sSL "https://raw.githubusercontent.com/sysread/bash-gpt/main/$tool" -o "/usr/local/bin/$tool" \
    && chmod +x "/usr/local/bin/$tool"
done
```

## From repo

1. Check out repository:
```bash
git clone https://github.com/sysread/bash-gpt
```
2. Add to your PATH
```bash
export PATH="$PATH:/path/to/bash-gpt"
```

## With [Nix](https://nixos.org/)
_Want an easy way to install nix? Checkout the [Determinite Systems Installer](https://github.com/DeterminateSystems/nix-installer)_

1. Check out repository:
```bash
git clone https://github.com/sysread/bash-gpt
```
2. Add Env Vars to your shell environment
- create a `.env` file with the following keys:
```bash
OPENAI_API_KEY=""
OPENAI_API_MODEL="gpt-3.5-turbo-16k"
```
- `OPENAI_API_KEY` - your OpenAI API key (required; create one at https://beta.openai.com/account/api-keys)
- `OPENAI_MODEL` - the [model](https://platform.openai.com/docs/models) to use; defaults to gpt-3.5-turbo-16k
3. Run the nix shell
```bash
nix develop
```


## Utils

To use the utilities in `utils`, first download it:

```bash
curl -sSL https://raw.githubusercontent.com/sysread/bash-gpt/main/utils -o /usr/local/lib/bash-gpt-utils
```

Then add it to your `.bashrc` or `.bash_profile`:
```bash
echo 'source /usr/local/lib/bash-gpt-utils' >> ~/.bashrc
```

# Environment variables

- `OPENAI_API_KEY` - your OpenAI API key (required; create one at https://beta.openai.com/account/api-keys)
- `OPENAI_MODEL` - the [model](https://platform.openai.com/docs/models) to use; defaults to gpt-3.5-turbo-16k
- `BASH_GPT_CONVERSATION_HISTORY_DIR` - the directory to save `chat` conversations. Defaults to $HOME/.gpt/conversations 
- `BASH_GPT_CHAT_HOME` - the directory to save `chat-beta` chats as well as assistant files. Defaults to `$HOME/.bashgpt/chat`

# Support and compatibility

Tested on macOS. Ought to work on linux as well. Please file a ticket if you
find an issue on mac or linux. Better yet, send me a pull request :)
