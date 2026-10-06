# Experiment 2 — LEX Program for Identifier, Number, and Keyword

## Aim
Implement a LEX program to recognize **keywords, integers, identifiers, and unknown characters**.

## File required
Create:

```text
exp2.l
```

## Step 1 — Open Command Prompt
Go to the folder containing the LEX file.

```cmd
cd C:\Your\Experiment\Folder
```

## Step 2 — Create the LEX file
Create `exp2.l` and paste this supplied program without changing it:

```lex
%{
#include <stdio.h>
%}

%%
if|else|while|for|int|float     { printf("Keyword : %s\n", yytext); }

[0-9]+                          { printf("Integer : %s\n", yytext); }

[A-Za-z_][A-Za-z0-9_]*          { printf("Identifier : %s\n", yytext); }

[ \t\n]+                        ;

.                               { printf("Unknown : %s\n", yytext); }
%%

int yywrap()
{
    return 1;
}

int main()
{
    printf("Enter input:\n");
    yylex();
    return 0;
}
```

## Step 3 — Generate the lexer

```cmd
flex exp2.1
```

This creates:

```text
lex.yy.c
```

## Step 4 — Compile

```cmd
gcc lex.yy.c -o exp2.exe
```

## Step 5 — Run

```cmd
exp2.exe
```

## Step 6 — Enter test input
The program accepts input from the console. Try an input containing keywords, numbers, and identifiers, for example:

```text
int a 123 while count
```

Press **Enter** after entering the input.

## Supplied regular-expression section
The source document also contains this separate LEX rules section. It is preserved below exactly as supplied; it is not presented as a complete standalone executable file because the source document does not provide its surrounding C sections.

```lex
%%
[0-9]+                  { printf("INTEGER\n"); }
[A-Za-z][A-Za-z0-9]*    { printf("IDENTIFIER\n"); }
if                      { printf("KEYWORD\n"); }
[ \t\n]+                ;
%%
```

## Quick exam commands

```cmd
flex exp2.l
gcc lex.yy.c -o exp2.exe
exp2.exe
```

## Result
The LEX program tokenizes the input into keywords, integers, identifiers, whitespace, and unknown characters.
