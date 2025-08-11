
# Semantic Versioning
<MAJOR>.<MINOR>.<PATCH>

if signature(arguments and returns) does not change, it is not a version change

If the change, changes signature and breaks previous versions, then it is a version changes. Breaking changes

Certain type, if you dont 

Minor: extend an API
Major: 

#Zshc
apt install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

code .zshrc 
plugins=(git python pyenv virtualenv pip zsh-autosuggestions zsh-syntax-highlighting)

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh)
[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh)
# Pyenv

pyenv versions: lists versions installed
pyenv install --list
pyenv install 3.12.6
pyenv shell 3.12.6
pyenv global 3.12.6


# Vscode

Ctrl+B hide side bar
Ctrl+D adds 

alt to move
alt + shift to ducplicate line


ctrl + space. Autocompletion

# virtual env
which python

python -m venv ./venv/
source venv/bin/activate  
deactivate 

Add the virtual environment to VScode by selecting it in the bottom menu.

# Git
- Repository: folder managed by git
- commits : collection of file addition or deletions

 git remote -v
  

1. In the new brach, first merge the master branch
2. merge new brach into master
3. Best practice, clean branches

**Material**  
- [Visualizing Git](https://git-school.github.io/visualizing-git/)
- [Learning Git](https://learngitbranching.js.org/)

# Code Review

[Python style guide](https://peps.python.org/pep-0008/)  
[**Google style guide**](https://google.github.io/styleguide/pyguide.html)

[Clean Code](https://testdriven.io/blog/clean-code-python/)

four questions for code reviewers
1. is the code desired?
2. is the code clean?
3. is the code safe?
4. does the code work?

## Automated code review

Black for automated code formatting
1.  install microst black extension
2.  Add preferences  
    
```json
{
  "editor.rulers": [
    {
      "color": "#ADD8E6",
      "column": 79
    },
    {
      "color": "#90EE90",
      "column": 99
    }
  ],
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.formatOnSave": true
  },
  "black-formatter.args": [
    "--line-length=99"
  ]
}
```

## Pylint

Linter makes more quality recommendations about code.

pylint --generate-rcfile > .pylintrc

Example to disable a error message from pylint
```python

# pylint: disable=too-many-instance-attributes, C0115
```

or in the extension

Flake 8

1. install Pylint extension
2. install isort extension
3. mypy

## Quality metrics
1. Radon
2. mccabe

## Typing
Python 3.5 adds the type of variables

`__` is pronounced dunder

[Type refrence](https://mypy.readthedocs.io/en/stable/builtin_types.html)

```python

from typing import Optional

maybe_num: Optional[int] = None
maybe_num = 1

# OR

from typing import Union
maybe_num: Union[int, None] = None

maybe_num = 1

# Python 3.10+
maybe_num: int | None = None
maybe_num = 1


from typing import TypedDict


class SalesSummary(TypedDict):
    sales: int
    country: str
    product_codes: list[str]


def get_sales_summary() -> SalesSummary:
    """Return summary for yesterday’s sales."""
    return {
        "sales": 1_000,
        "country": "UK",
        "product_codes": ["SUYDT"],
    }
```

```python
from dataclasses import dataclass

@dataclass
class InventoryItem:
    """Class for keeping track of an item in inventory."""
    name: str
    unit_price: float
    quantity_on_hand: int = 0

    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand
```

## Code Revision

`#!` "shebang" is a contraction of "sharp bang" or "hash bang".   

1. Sharp or Hash: This refers to the # character, often called "sharp" (as in music) or "hash" (like the symbol on a telephone keypad).
2. Bang: This refers to the ! character, often called "bang" (perhaps because of its explosive-sounding name!).


```bash
pip install pre-commit
pre-commit install
pre-commit autoupdate
```

# Package
Packaging, approaches
1. version 1
   1. Module: any py file
   2. package: folder with  __init__.py
   3. subpackage: package within a folder
   4. distribution package: zip file with packages
   5. setup.py: 
   6. distutils: 

Any new venv has to packages preinstalled, pip and setuptools

Setup.py
```python
from setuptools import setup

setup(
  name=''
)
```

```bash
python setup.py build sdist
```

## shortcoming of sdist
1. may make assumptions about customer machine
2. is slow setup.py may require compilation
3. is insecure, malicious code may be included

## wheel
pip install wheel
python setup.py bdist_wheel

# Project

```bash
python -m venv projectv
source projectv/bin/activate
pip install cookiecutter
```

# References
[Course material](https://ericriddoch.notion.site/Taking-Python-to-Production-A-Professional-Onboarding-Guide-799409731bf14c78a531ac779f1bd76d)

[Notebooks precommit](https://levelup.gitconnected.com/pre-commit-hooks-for-data-science-projects-boost-code-quality-in-your-repositories-65285ae4db21)


