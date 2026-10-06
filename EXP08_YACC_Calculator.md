# Experiment 8 — Calculator Using YACC

## Aim
Write a program for implementation of calculator using YACC tool.

## Important source note
The uploaded Experiment 8 document contains the experiment title and descriptions of special LEX/YACC functions, but **does not contain the actual calculator source code or a complete set of build commands**. This file therefore does not invent or replace the missing program.

## Step 1 — Confirm the environment

```cmd
bison --version
flex --version
gcc --version
```

## Step 2 — Use the exact calculator source supplied by your lab/teacher
Create the exact `.l` and `.y` files provided with the experiment.

For an example file naming scheme, the build sequence would be:

```text
calc.l
calc.y
```
```calc.l file
%{
#include "calc.tab.h"
#include <stdlib.h>
%}

%%
[0-9]+      { yylval = atoi(yytext); return NUMBER; }
[ \t]       ;
\n          { return '\n'; }
[+\-*/()]   { return yytext[0]; }
.           { return yytext[0]; }
%%

int yywrap()
{
    return 1;
}
```
 ```calc.y file
%{
#include <stdio.h>

int yylex();
void yyerror(const char *s)
{
    printf("Invalid Expression\n");
}
%}

%token NUMBER

%%
input:
    expr '\n' { printf("Result = %d\n", $1); }
    ;

expr:
      expr '+' term { $$ = $1 + $3; }
    | expr '-' term { $$ = $1 - $3; }
    | term          { $$ = $1; }
    ;

term:
      term '*' factor { $$ = $1 * $3; }
    | term '/' factor { $$ = $1 / $3; }
    | factor          { $$ = $1; }
    ;

factor:
      '(' expr ')' { $$ = $2; }
    | NUMBER       { $$ = $1; }
    ;
%%

int main()
{
    printf("Enter expression: ");
    yyparse();
    return 0;
}
```


## Step 3 — Generate the parser

```cmd
bison -d calc.y
```

## Step 4 — Generate the scanner

```cmd
flex calc.l
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
bison -d calc.y
flex calc.l
gcc calc.tab.c lex.yy.c -o calc.exe
calc.exe
```

> The exact calculator source is missing from the uploaded Experiment 8 document, so the `.l`/`.y` file contents and exact file names must be taken from your original lab material.
