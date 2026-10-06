# Experiment 7 — Generate Three-Address Code Using LEX and YACC

## Aim
Generate three-address code for a simple program using LEX and YACC.

## Files required
The supplied LEX section includes `#include "ex4.tab.h"`, and the supplied YACC section is named `ex4.y`. Therefore keep these file names:

```text
ex4.l
ex4.y
```

## Step 1 — Create `ex4.l`
Paste the supplied LEX part exactly:

```lex
%{
#include<stdio.h>
#include "ex4.tab.h"
%}
%%

[0-9]+ {yylval=atoi(yytext);return NUM;}
[\t] ;
\n {return EOL;}
[-+*/()] {return yytext[0];}
. {fprintf(stderr,"Error:Invalid Character\n");}
%%
int yywrap(){
return 1;
}
```

## Step 2 — Create `ex4.y`
Paste the supplied YACC part exactly:

```yacc
%{
#include<stdio.h>
#include<stdlib.h>
int temp_count=0;
void yyerror(const char*s){
fprintf(stderr,"Error:%s\n",s);
}
%}
%token NUM EOL
%left '+' '-'
%left '*' '/'
%%
program:lines
;
lines:lines line
| line
;
line:expr EOL

{
printf("Result:t%d\n",$1);
}
;
expr:NUM{
$$=$1;
}
| '(' expr ')'
{
$$=$2;
}
| expr '+' expr
{
printf("t%d=%d+%d\n",++temp_count,$1,$3);
$$=temp_count;
}
| expr '-' expr
{
printf("t%d=%d-%d\n",++temp_count,$1,$3);
$$=temp_count;
}
| expr '*' expr
{
printf("t%d=%d*%d\n",++temp_count,$1,$3);
$$=temp_count;
}
| expr '/' expr
{
if($3==0)
{yyerror("Division by zero");

$$=0;}
else{
printf("t%d=%d/%d\n",++temp_count,$1,$3);
$$=temp_count;
}
}
;
%%
int main()
{
yyparse();
return 0;
}
```

## Step 3 — Open Command Prompt in the experiment folder

```cmd
cd C:\Your\Experiment\Folder
```

## Step 4 — Generate the parser

```cmd
bison -d ex4.y
```

This creates:

```text
ex4.tab.c
ex4.tab.h
```

## Step 5 — Generate the scanner

```cmd
flex ex4.l
```

This creates:

```text
lex.yy.c
```

## Step 6 — Compile

```cmd
gcc lex.yy.c ex4.tab.c -o ex4.exe
```

## Step 7 — Run

```cmd
./ex4.exe
```

## Step 8 — Test input
Use the supplied example:

```text
2*10/2+5-1
```

The supplied document shows the following example output:

```text
t1=2*10
t2=1/2
t3=2+5
t4=3-1
Result:t4
```

## Quick exam commands

```cmd
bison -d ex4.y
flex ex4.l
gcc lex.yy.c ex4.tab.c -o ex4.exe
./ex4.exe
```

## Result
The LEX and YACC program generates intermediate three-address instructions while parsing the arithmetic expression.
