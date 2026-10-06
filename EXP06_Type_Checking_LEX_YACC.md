# Experiment 6 — Type Checking Using LEX and YACC

## Aim
Implement type checking using LEX and YACC.

## Files required
Create these two files:

```text
type.l
type.y
```

## Step 1 — Create `type.l`
Use the supplied LEX part exactly as given:

```lex
%{
#include "type.tab.h"
%}
%%
[0-9]+ {
yylval = atoi(yytext);
return INTEGER;
}
[0-9]+"."[0-9]* {
yylval = atof(yytext);
return FLOAT;
}
[a-zA-Z]+ {
yylval= yytext;
return CHAR;
}
[ \t] ; // Ignore whitespace and tabs
\n { return EOL; } // Newline character
. { return yytext[0]; } // Return other characters as is
%%
int yywrap() {
return 1;
}
```

## Step 2 — Create `type.y`
Use the supplied YACC part exactly as given:

```yacc
%{
#include <stdio.h>
void yyerror(const char* s) {
fprintf(stderr, "Parse error: %s\n", s);
}
int yylex(); // Declare the lexer function
%}
%token INTEGER FLOAT CHAR EOL
%%

program:
/* empty */
| program line
;
line:
statement EOL {
if ($1 == INTEGER) {
printf("Type: INTEGER\n");
} else if ($1 == FLOAT) {
printf("Type: FLOAT\n");
}
else if ($1 == CHAR) {
printf("Type: CHAR/STRING\n");
} else {
printf("Invalid type\n");
}
}
;
statement:
expression {
$$ = $1;
}
;
expression:
INTEGER {
$$ = INTEGER;
}
| FLOAT {
$$ = FLOAT;
}
| CHAR {
$$ = CHAR;
}
;
%%
int main() {
yyparse();
return 0;
}
```

## Step 3 — Open Command Prompt
Go to the folder containing both files.

```cmd
cd C:\Your\Experiment\Folder
```

## Step 4 — Generate the parser

```cmd
win_bison -d type.y
```

This creates:

```text
type.tab.c
type.tab.h
```

## Step 5 — Generate the scanner

```cmd
win_flex type.l
```

This creates:

```text
lex.yy.c
```

## Step 6 — Compile

```cmd
gcc type.tab.c lex.yy.c -o type.exe
```

## Step 7 — Run

```cmd
type.exe
```

## Step 8 — Test values from the supplied experiment

```text
123
123.897
God
df24
```

Expected supplied results include:

```text
Type:INTEGER
Type:FLOAT
Type:CHAR/STRING
Invalid type or Parse error
```

## Quick exam commands

```cmd
win_bison -d type.y
win_flex type.l
gcc type.tab.c lex.yy.c -o type.exe
type.exe
```

## Result
The supplied LEX and YACC program classifies input into integer, float, and character/string types and reports invalid input through the parser.
