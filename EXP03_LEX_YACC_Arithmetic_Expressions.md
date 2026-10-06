# Experiment 3 — YACC (Bison) Program for Valid Arithmetic Expressions

## Aim
Recognize valid arithmetic expressions containing `+`, `-`, `*`, `/`, and parentheses using LEX and YACC.

## Software required
- WinFlexBison
- GCC (MinGW/MSYS2)
- Command Prompt or PowerShell

## One-time WinFlexBison setup
Use the supplied procedure:

1. Download WinFlexBison from the official release page: `https://github.com/lexxmark/winflexbison/releases`
2. Extract it, for example, to `C:\winflexbison\`.
3. Add `C:\winflexbison\` to the system `Path`.
4. Open a **new** Command Prompt.
5. Verify:

```cmd
win_bison --version
win_flex --version
```

Both commands should show version information.

## Files required
Create these two files in the same folder:

```text
expr.l
expr.y
```

## Step 1 — Create `expr.l`

```lex
%{
#include "expr.tab.h"
%}

%%
[0-9]+                  { return NUMBER; }
[a-zA-Z][a-zA-Z0-9]*    { return ID; }

"+"     { return '+'; }
"-"     { return '-'; }
"*"     { return '*'; }
"/"     { return '/'; }
"("     { return '('; }
")"     { return ')'; }

[ \t]   ;
\n       { return '\n'; }

.        { return yytext[0]; }
%%

int yywrap()
{
    return 1;
}
```

## Step 2 — Create `expr.y`

```yacc
%{
#include <stdio.h>
#include <stdlib.h>

int yylex();
void yyerror(const char *s);
%}

%token ID NUMBER

%%

input:
      expr '\n'
      {
          printf("Valid Arithmetic Expression\n");
      }
      ;

expr:
      expr '+' term
    | expr '-' term
    | term
    ;

term:
      term '*' factor
    | term '/' factor
    | factor
    ;

factor:
      '(' expr ')'
    | ID
    | NUMBER
    ;

%%

void yyerror(const char *s)
{
    printf("Invalid Arithmetic Expression\n");
}

int main()
{
    printf("Enter Arithmetic Expression:\n");
    yyparse();
    return 0;
}
```

## Step 3 — Open Command Prompt in the experiment folder

```cmd
cd C:\Your\Experiment\Folder
```

## Step 4 — Generate the YACC parser

```cmd
win_bison -d expr.y
```

This creates:

```text
expr.tab.c
expr.tab.h
```

## Step 5 — Generate the LEX scanner

```cmd
win_flex expr.l
```

This creates:

```text
lex.yy.c
```

## Step 6 — Compile

```cmd
gcc expr.tab.c lex.yy.c -o expr.exe
```

The supplied document also gives this fallback when a linker error occurs:

```cmd
gcc expr.tab.c lex.yy.c -o expr.exe -lfl
```

## Step 7 — Run

```cmd
expr.exe
```

## Step 8 — Test
Valid example:

```text
a+b*c
```

Expected:

```text
Valid Arithmetic Expression
```

Invalid example:

```text
a+*b
```

Expected:

```text
Invalid Arithmetic Expression
```

## Quick exam commands

```cmd
win_bison -d expr.y
win_flex expr.l
gcc expr.tab.c lex.yy.c -o expr.exe
expr.exe
```

## Result
LEX performs tokenization and YACC performs syntax analysis to determine whether the arithmetic expression is valid.
