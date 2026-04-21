# Zsh and Oh My Zsh Complete Setup Guide

This guide provides step-by-step instructions on how to install Zsh, Oh My Zsh, and essential extensions for both macOS and Debian-based Linux distributions, based on the notes in `python_prod_notes.md`.

## 1. Install Zsh

Depending on your operating system, use the appropriate package manager to install Zsh.

### Debian/Ubuntu-based Linux
```bash
sudo apt update
sudo apt install zsh
```

### macOS
*(Note: Zsh is the default shell on macOS Catalina and newer, but you can install/update it via Homebrew)*
```bash
brew install zsh
```

---

## 2. Install Oh My Zsh

Oh My Zsh is an open-source, community-driven framework for managing your Zsh configuration. Run the following command in your terminal to install it:

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

---

## 3. Install Essential Extensions

We'll install two highly recommended plugins for a better terminal experience:
- **zsh-autosuggestions**: Suggests commands as you type based on your history and completions.
- **zsh-syntax-highlighting**: Highlights commands while they are typed at a zsh prompt into green if they are valid commands, or red if they are not.

To install them, clone their repositories into the Oh My Zsh custom plugins folder:

### 3.1 Install `zsh-autosuggestions`
[Official zsh-autosuggestions oh-my-zsh Install Guide](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh)
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### 3.2 Install `zsh-syntax-highlighting`
[Official zsh-syntax-highlighting oh-my-zsh Install Guide](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh)
```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

---

## 4. Configure Extensions in `.zshrc`

After downloading the extensions, you must enable them in your Zsh configuration file (`~/.zshrc`).

1. Open the `.zshrc` file in your preferred editor (e.g., VS Code or nano):
   ```bash
   code ~/.zshrc
   # OR
   nano ~/.zshrc
   ```

2. Look for the `plugins=(...)` line. By default, it usually only has `git` listed.

3. Update the line to include the new plugins, along with other tools mentioned in the notes:
   ```bash
   plugins=(git python pyenv virtualenv pip zsh-autosuggestions zsh-syntax-highlighting)
   ```

*(Note: Ensure plugins are separated by spaces inside the parentheses, not commas).*

---

## 5. Install and Configure pyenv

`pyenv` lets you easily switch between multiple versions of Python.

### 5.1 Install pyenv

Depending on your operating system, install pyenv:

**macOS:**
```bash
brew install pyenv
```

**Debian/Ubuntu-based Linux:**
*(Ensure you have the build dependencies installed first, then run the installer)*
```bash
sudo apt update; sudo apt install -y build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev curl libncursesw5-dev \
xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
curl https://pyenv.run | bash
```

### 5.2 Configure `.zshrc` for pyenv

To ensure pyenv is set up automatically every time you open a terminal session, you need to add its initialization commands to your `.zshrc` file.

Open your `~/.zshrc` file, or simply execute the following command to append the configuration:

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init - zsh)"' >> ~/.zshrc
```

Alternatively, you can open your `~/.zshrc` manually and add these lines to the end of the file:
```bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init - zsh)"
```

---

## 6. Apply the Changes

For the changes to take effect immediately, you can either close and reopen your terminal session or reload your `.zshrc` file using the following command:

```bash
source ~/.zshrc
```

Your terminal is now successfully enhanced!
