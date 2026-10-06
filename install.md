# PCD Lab — WSL Installation Guide

This guide installs all required tools for running the Compiler Design (PCD) experiments in WSL/Linux.

> **Important:** These commands are for WSL/Linux. Do **not** use `win_flex` or `win_bison`.

## Step 1 — Update Package Lists

```bash
sudo apt update
```

## Step 2 — Install Required Tools

```bash
sudo apt install -y build-essential gcc g++ make flex bison
```

### Installed tools

| Tool | Purpose |
|---|---|
| `gcc` | Compile C programs |
| `g++` | Compile C++ programs |
| `flex` | Generate scanners from `.l` files |
| `bison` | Generate parsers from `.y` files |
| `make` | Build automation |
| `build-essential` | Common compilation tools |

## Step 3 — Verify Installation

```bash
gcc --version
flex --version
bison --version
make --version
```

If all commands display version information, the installation is complete.

---

# PCD Experiment Commands

## LEX Program

```bash
flex program.l
gcc lex.yy.c -o program
./program
```

Example:

```bash
flex automata.l
gcc lex.yy.c -o automata
./automata
```

## LEX + YACC/Bison Program

```bash
bison -d program.y
flex program.l
gcc program.tab.c lex.yy.c -o program
./program
```

Example:

```bash
bison -d calc.y
flex calc.l
gcc calc.tab.c lex.yy.c -o calc
./calc
```

## C Program

```bash
gcc program.c -o program
./program
```

Example:

```bash
gcc optimization.c -o optimization.exe
./optimization.exe
```

> In WSL, use `./` when running an executable from the current directory.

---

# One-Command Installation

```bash
sudo apt update && sudo apt install -y build-essential flex bison
```

# Important

Use these in WSL:

```text
flex
bison
gcc
```

Do **not** use:

```text
win_flex
win_bison
```
