# Experiment 8 — Calculator Using YACC

## Aim
Write a program for implementation of calculator using YACC tool.

## Important source note
The uploaded Experiment 8 document contains the experiment title and descriptions of special LEX/YACC functions, but **does not contain the actual calculator source code or a complete set of build commands**. This file therefore does not invent or replace the missing program.

## Step 1 — Confirm the environment

```cmd
win_bison --version
win_flex --version
gcc --version
```

## Step 2 — Use the exact calculator source supplied by your lab/teacher
Create the exact `.l` and `.y` files provided with the experiment.

For an example file naming scheme, the build sequence would be:

```text
calc.l
calc.y
```

Do not substitute another calculator program when the requirement is to reproduce the same experiment.

## Step 3 — Generate the parser

```cmd
win_bison -d calc.y
```

## Step 4 — Generate the scanner

```cmd
win_flex calc.l
```

## Step 5 — Compile

```cmd
gcc calc.tab.c lex.yy.c -o calc.exe
```

## Step 6 — Run

```cmd
calc.exe
```

## Special functions listed in the supplied Experiment 8 document

- `yytext` — text matched most recently is stored
- `yyleng` — number of characters in text most recently matched
- `yylval` — associated value of current token
- `yymore()` — append next string matched to current contents of `yytext`
- `yyless(n)` — remove from `yytext` all but the first `n` characters
- `unput(c)` — return a character to the input stream
- `yywrap()` — called by the lexical analyzer when it reaches EOF while trying to match a regular expression

## Quick exam sequence

```cmd
win_bison -d calc.y
win_flex calc.l
gcc calc.tab.c lex.yy.c -o calc.exe
calc.exe
```

> The exact calculator source is missing from the uploaded Experiment 8 document, so the `.l`/`.y` file contents and exact file names must be taken from your original lab material.
