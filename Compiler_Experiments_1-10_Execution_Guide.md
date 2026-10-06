# Compiler Design Experiments 1–10 — Exam Execution Guide

> **Purpose:** Quick GitHub/exam reference for executing the same practicals from the supplied lab documents. The program code below is reproduced from the uploaded documents without intentional code changes. Execution commands and file-management steps are added so the experiments can be run from Command Prompt/PowerShell.

## 0. One-time setup

### Required tools
1. GCC (MinGW/MSYS2)
2. Flex
3. WinFlexBison (`win_flex`, `win_bison`) for YACC/Bison experiments
4. Command Prompt or PowerShell

### Check installation
```bat
gcc --version
flex --version
win_bison --version
win_flex --version
```

### WinFlexBison setup from the supplied lab document
1. Download WinFlexBison from the release page listed in the lab document.
2. Extract it, for example, to `C:\winflexbison\`.
3. Add `C:\winflexbison\` to the system `Path`.
4. Open a **new** Command Prompt.
5. Confirm `win_bison --version` and `win_flex --version` work.

### Recommended folder structure
```text
compiler-experiments/
├── EXP1/   automata.l
├── EXP2/   exp2.l
├── EXP3/   expr.l  expr.y
├── EXP4/   exp4.c
├── EXP5/   sdd.l
├── EXP6/   type.l  type.y
├── EXP7/   ex4.l  ex4.y
├── EXP8/   (calculator source not included in supplied DOCX)
├── EXP9/   opt.c
└── EXP10/  opt10.c
```

---

## Experiment 1 — Finite Automata for Regular Languages

### Objective
Implement a finite automaton that accepts strings over `{a,b}` that start and end with `a`.

### Step 1 — Create `automata.l`
```lex
%{
#include <stdio.h>
/* Global tracking flags */
int is_valid = 0;
%}

%%
^a(a|b)*a$    { is_valid = 1; }  /* Matches lines starting and ending with 'a' */
^a$           { is_valid = 1; }  /* Single character 'a' starts and ends with 'a' */
\n            { return 0; }      /* Stop processing when Enter/Newline is hit */
.             { is_valid = 0; }  /* Any other invalid characters break the state */
%%

int main() {
    printf("--- Finite Automata Simulator ---\n");
    printf("Language: Strings over {a,b} starting and ending with 'a'\n");
    printf("Enter your string: ");
    
    yylex(); /* Starts the state engine analysis loop */

    if (is_valid) {
        printf("\nResult: STRINGS ACCEPTED (Reached Final/Accepting State)\n");
    } else {
        printf("\nResult: STRING REJECTED (Dead End/Invalid State)\n");
    }
    
    return 0;
}

int yywrap() {
    return 1;
}
```

### Step 2 — Open Command Prompt and go to the experiment folder
```bat
cd /d C:\path\to\compiler-experiments\EXP1
```

### Step 3 — Generate the lexer
```bat
flex automata.l
```

### Step 4 — Compile with GCC
```bat
gcc lex.yy.c -o automata.exe
```

### Step 5 — Run
```bat
automata.exe
```

### Step 6 — Test
- `aba` → accepted
- `abbbba` → accepted
- `ab` → rejected
- `baba` → rejected

---

## Experiment 2 — LEX: Identifier, Number and Keyword

### Step 1 — Create `exp2.l` and paste the first LEX program from the lab sheet
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

### Step 2 — Generate the lexer
```bat
cd /d C:\path\to\compiler-experiments\EXP2
flex exp2.l
```

### Step 3 — Compile
```bat
gcc lex.yy.c -o exp2.exe
```

### Step 4 — Run
```bat
exp2.exe
```

### Step 5 — Enter input
Type input containing keywords, numbers and identifiers, then press Enter.

### Regular-expression snippet also present in the supplied EXP2 document
```lex
%%
[0-9]+                  { printf("INTEGER\n"); }
[A-Za-z][A-Za-z0-9]*    { printf("IDENTIFIER\n"); }
if                      { printf("KEYWORD\n"); }
[ \t\n]+                ;
```
> The second snippet is a rule-only fragment in the supplied document, not a complete standalone lexer program.

---

## Experiment 3 — YACC/Bison Arithmetic Expression Validation

### Step 1 — Create `expr.l`
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

### Step 2 — Create `expr.y`
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

### Step 3 — Open Command Prompt in the folder
```bat
cd /d C:\path\to\compiler-experiments\EXP3
```

### Step 4 — Generate the parser
```bat
win_bison -d expr.y
```
Expected generated files: `expr.tab.c`, `expr.tab.h`.

### Step 5 — Generate the scanner
```bat
win_flex expr.l
```
Expected generated file: `lex.yy.c`.

### Step 6 — Compile
```bat
gcc expr.tab.c lex.yy.c -o expr.exe
```
If the linker reports a Flex library error, the supplied document gives:
```bat
gcc expr.tab.c lex.yy.c -o expr.exe -lfl
```

### Step 7 — Run
```bat
expr.exe
```

### Step 8 — Test
- `a+b*c` → Valid Arithmetic Expression
- `a+*b` → Invalid Arithmetic Expression

---

## Experiment 4 — LL(1) Parser for Arithmetic Expressions

### Step 1 — Create `exp4.c`
```c

#include <stdio.h>
#include <stdlib.h>

char input[100];
int pos = 0;

void E();
void EP();
void T();
void TP();
void F();

void error()
{
    printf("\nString Rejected\n");
    exit(0);
}

void match(char c)
{
    if(input[pos] == c)
        pos++;
    else
        error();
}

void E()
{
    T();
    EP();
}

void EP()
{
    if(input[pos] == '+')
    {
        match('+');
        T();
        EP();
    }
}

void T()
{
    F();
    TP();
}

void TP()
{
    if(input[pos] == '*')
    {
        match('*');
        F();
        TP();
    }
}

void F()
{
    if(input[pos] == 'i')
    {
        match('i');
    }
    else if(input[pos] == '(')
    {
        match('(');
        E();
        match(')');
    }
    else
    {
        error();
    }
}

int main()
{
    printf("Enter expression: ");
    scanf("%s", input);

    E();

    if(input[pos] == '\0')
        printf("\nString Accepted\n");
    else
        printf("\nString Rejected\n");

    return 0;
}
```

### Step 2 — Compile
```bat
cd /d C:\path\to\compiler-experiments\EXP4
gcc exp4.c -o exp4.exe
```

### Step 3 — Run
```bat
exp4.exe
```

### Step 4 — Test
- `i+i*i` → String Accepted
- `(i+i)*i` → String Accepted

---

## Experiment 5 — SDD for Evaluating Arithmetic Expressions Using LEX

### Step 1 — Create `sdd.l`
```lex
%{
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

#define MAX 100

int values[MAX];
char operators[MAX];

int vtop = -1;
int otop = -1;

void pushValue(int value)
{
    values[++vtop] = value;
}

int popValue()
{
    return values[vtop--];
}

void pushOperator(char op)
{
    operators[++otop] = op;
}

char popOperator()
{
    return operators[otop--];
}

int precedence(char op)
{
    if (op == '+' || op == '-')
        return 1;

    if (op == '*' || op == '/')
        return 2;

    return 0;
}

void applyOperator()
{
    int a, b, result;
    char op;

    b = popValue();
    a = popValue();
    op = popOperator();

    switch (op)
    {
        case '+':
            result = a + b;
            break;

        case '-':
            result = a - b;
            break;

        case '*':
            result = a * b;
            break;

        case '/':
            if (b == 0)
            {
                printf("Error: Division by zero\n");
                exit(1);
            }
            result = a / b;
            break;

        default:
            printf("Invalid operator\n");
            exit(1);
    }

    pushValue(result);
}

void processOperator(char op)
{
    while (otop >= 0 &&
           operators[otop] != '(' &&
           precedence(operators[otop]) >= precedence(op))
    {
        applyOperator();
    }

    pushOperator(op);
}

void processRightParenthesis()
{
    while (otop >= 0 && operators[otop] != '(')
    {
        applyOperator();
    }

    if (otop < 0)
    {
        printf("Error: Mismatched parentheses\n");
        exit(1);
    }

    popOperator();
}
%}

%%

[0-9]+      {
                pushValue(atoi(yytext));
            }

[+]         {
                processOperator('+');
            }

[-]         {
                processOperator('-');
            }

[*]         {
                processOperator('*');
            }

[/]         {
                processOperator('/');
            }

[(]         {
                pushOperator('(');
            }

[)]         {
                processRightParenthesis();
            }

[ \t]+      ;

\n          {
                while (otop >= 0)
                {
                    if (operators[otop] == '(')
                    {
                        printf("Error: Mismatched parentheses\n");
                        exit(1);
                    }

                    applyOperator();
                }

                if (vtop == 0)
                {
                    printf("Result = %d\n", values[vtop]);
                }
                else
                {
                    printf("Error: Invalid expression\n");
                }

                vtop = -1;
                otop = -1;
            }

.           {
                printf("Invalid character: %s\n", yytext);
            }

%%

int yywrap()
{
    return 1;
}

int main()
{
    printf("Enter an arithmetic expression:\n");
    yylex();

    return 0;
}
```

### Step 2 — Generate the lexer
```bat
cd /d C:\path\to\compiler-experiments\EXP5
flex sdd.l
```

### Step 3 — Compile
```bat
gcc lex.yy.c -o sdd.exe
```

### Step 4 — Run
```bat
sdd.exe
```

### Step 5 — Enter an arithmetic expression
Type an expression supported by the grammar and press Enter. The program prints `Result = ...`.

### Core grammar from the lab sheet
```text
E → E + T
E → E - T
E → T
T → T * F
T → T / F
T → F
F → (E)
F → NUM
```

---

## Experiment 6 — Type Checking Using LEX and YACC

### Step 1 — Create `type.l`
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

### Step 2 — Create `type.y`
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

### Step 3 — Open Command Prompt in EXP6
```bat
cd /d C:\path\to\compiler-experiments\EXP6
```

### Step 4 — Generate the parser header/source
```bat
win_bison -d type.y
```

### Step 5 — Generate the lexer
```bat
win_flex type.l
```

### Step 6 — Compile
```bat
gcc type.tab.c lex.yy.c -o type.exe
```

### Step 7 — Run
```bat
type.exe
```

### Step 8 — Reference output from the supplied document
```text
Output:
123
Type:INTEGER
123.897
Type:FLOAT
God
Type:CHAR/STRING
df24
Invalid type or Parse error
```

---

## Experiment 7 — Three-Address Code Using LEX and YACC

### Step 1 — Create `ex4.l`
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

### Step 2 — Create `ex4.y`
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

### Step 3 — Generate the parser
```bat
cd /d C:\path\to\compiler-experiments\EXP7
win_bison -d ex4.y
```

### Step 4 — Generate the lexer
```bat
win_flex ex4.l
```

### Step 5 — Compile
```bat
gcc ex4.tab.c lex.yy.c -o ex4.exe
```

### Step 6 — Run
```bat
ex4.exe
```

### Step 7 — Test with the supplied example
```text
OUTPUT:

2*10/2+5-1
t1=2*10
t2=1/2
t3=2+5
t4=3-1
Result:t4
```

---

## Experiment 8 — Calculator Using YACC

### Source-status note
The uploaded EXP8 document contains the experiment title, LEX/YACC special functions, and background description, but **does not contain the calculator source code or execution commands**. I therefore have not invented or altered a calculator program here.

### Special functions listed in the supplied document
```text
Special Functions 
• yytext– where text matched most recently is stored
 • yyleng– number of characters in text most recently matched • yylval– associated value of current token 
• yymore()– append next string matched to current contents of yytext • yyless(n)– remove from yytext all but the first n characters
 • unput(c) – return character c to input stream 
• yywrap()– may be replaced by user and the yywrap method is called by the lexical analyzer whenever it inputs an EOF as the first character when trying to match a regular expression
```

### Generic execution pattern (only after you have the actual lab `calculator.l` / `calculator.y` source)
```bat
cd /d C:\path\to\compiler-experiments\EXP8
win_bison -d calculator.y
win_flex calculator.l
gcc calculator.tab.c lex.yy.c -o calculator.exe
calculator.exe
```
> **Do not treat the generic filenames above as replacement code.** The supplied EXP8 document does not specify the actual filenames or program body.

---

## Experiment 9 — Code Optimization Techniques

### Important source note
The supplied EXP9 document describes optimization techniques and provides a C program. I have reproduced that program exactly as supplied, including the formatting/syntax appearing in the DOCX. Because several string literals are split across document lines and the source contains `pr[i].l='';`, the supplied source may not compile as-is. I have **not** silently corrected it.

### Step 1 — Create `opt.c`
```c
#include<stdio.h>
#include<string.h>
struct op
{
char l;
char r[20];
}
op[10],pr[10];
void main()
{
int a,i,k,j,n,z=0,m,q;
char *p,*l;
char temp,t;
char *tem;
printf("Enter the Number of Values:");
scanf("%d",&n);
for(i=0;i<n;i++)
{
printf("left: ");
scanf(" %c",&op[i].l);
printf("right: ");
scanf(" %s",&op[i].r);
}
printf("Intermediate Code
") ;
for(i=0;i<n;i++)
{
printf("%c=",op[i].l);
printf("%s
",op[i].r);
}
for(i=0;i<n-1;i++)
{
temp=op[i].l;
for(j=0;j<n;j++)
{
p=strchr(op[j].r,temp);
if(p)
{
pr[z].l=op[i].l;
strcpy(pr[z].r,op[i].
r);
z++;
}
}
}
pr[z].l=op[n-1].l;
strcpy(pr[z].r,op[n-1].r);
z++;
printf("
After Dead Code Elimination
");
for(k=0;k<z;k++)
{
printf("%c =",pr[k].l);
printf("%s
",pr[k].r);
}
for(m=0;m<z;m++)
{
tem=pr[m].r;
for(j=m+1;j<z;j++)
{
p=strstr(tem,pr[j].r);
if(p)
{
t=pr[j].l;
pr[j].l=pr[m].l;
for(i=0;i<z;i++)
{
l=strchr(pr[i].r,t) ;
if(l)
{
a=l-pr[i].r;
printf("pos: %d",a);
pr[i].r[a]=pr[m].l;
}}}}}
printf("Eliminate Common Expression");
for(i=0;i<z;i++)
{
printf("%c =",pr[i].l);
printf("%s",pr[i].r);
}
for(i=0;i<z;i++)
{
for(j=i+1;j<z;j++)
{
q=strcmp(pr[i].r,pr[j].r);
if((pr[i].l==pr[j].l)&&!q)
{
pr[i].l='';
}
}
}
printf("Optimized Code");
for(i=0;i<z;i++)
{
if(pr[i].l!='')
{
printf("%c=",pr[i].l);
printf("%s",pr[i].r);
}
}
}
```

### Step 2 — Compile
```bat
cd /d C:\path\to\compiler-experiments\EXP9
gcc opt.c -o opt.exe
```

### Step 3 — Run
```bat
opt.exe
```

### Step 4 — Follow the prompts
1. Enter the number of values.
2. Enter each left-hand variable.
3. Enter each right-hand expression.
4. Observe the intermediate code, dead-code elimination, common-expression handling, and optimized-code sections.

---

## Experiment 10 — Machine-Independent Optimization and Data-Flow Analysis

### Step 1 — Create `opt10.c`
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define MAX 50

typedef struct
{
    char code[100];
    int leader;
} Statement;

Statement stmt[MAX];
int n;

/* Check whether a statement contains a jump */
int isJump(char *s)
{
    return (strstr(s, "goto") != NULL ||
            strstr(s, "if") != NULL);
}

/* Extract jump target */
int getTarget(char *s)
{
    char *p;
    int target;

    p = strstr(s, "goto");

    if (p != NULL)
    {
        if (sscanf(p + 4, "%d", &target) == 1)
            return target;
    }

    return -1;
}

void findLeaders()
{
    int i, target;

    for (i = 0; i < n; i++)
        stmt[i].leader = 0;

    /* First statement is always a leader */
    stmt[0].leader = 1;

    for (i = 0; i < n; i++)
    {
        if (isJump(stmt[i].code))
        {
            /* Statement following jump */
            if (i + 1 < n)
                stmt[i + 1].leader = 1;

            /* Jump target */
            target = getTarget(stmt[i].code);

            if (target >= 1 && target <= n)
                stmt[target - 1].leader = 1;
        }
    }
}

void printBasicBlocks()
{
    int i, block = 1;

    printf("\nBASIC BLOCKS\n");
    printf("============\n");

    for (i = 0; i < n; i++)
    {
        if (stmt[i].leader)
        {
            printf("\nB%d:\n", block++);
        }

        printf("   %d: %s\n", i + 1, stmt[i].code);
    }
}

int main()
{
    int i;

    printf("Enter number of three-address statements: ");
    scanf("%d", &n);

    getchar();

    printf("\nEnter the statements:\n");

    for (i = 0; i < n; i++)
    {
        printf("%d: ", i + 1);
        fgets(stmt[i].code, sizeof(stmt[i].code), stdin);

        stmt[i].code[strcspn(stmt[i].code, "\n")] = '\0';
    }

    findLeaders();

    printBasicBlocks();

    printf("\nData-flow analysis can now be performed");
    printf(" on these basic blocks.\n");

    return 0;
}
```

### Step 2 — Compile
```bat
cd /d C:\path\to\compiler-experiments\EXP10
gcc opt10.c -o opt10.exe
```

### Step 3 — Run
```bat
opt10.exe
```

### Step 4 — Enter the supplied sample input
```text
 Sample Input
Enter number of three-address statements: 7

1: a = 10
2: b = 20
3: c = a + b
4: if c < 50 goto 6
5: c = c + 1
6: d = c * 2
7: print d
```

### Step 5 — Confirm the basic blocks
The supplied sample output separates the input into `B1`, `B2`, and `B3`.

### Step 6 — Follow the data-flow analysis sequence from the lab sheet
1. Read the three-address code.
2. Identify leaders.
3. Divide the program into basic blocks.
4. Construct the Control Flow Graph.
5. Determine `GEN[B]` and `KILL[B]`.
6. Initialize `IN[B]` and `OUT[B]`.
7. Apply the data-flow equations repeatedly.
8. Continue until the sets no longer change.
9. Use the information for optimization.
10. Display the optimized code.

---

## Quick command sheet

### Flex-only experiments
```bat
flex automata.l
gcc lex.yy.c -o automata.exe
automata.exe

flex exp2.l
gcc lex.yy.c -o exp2.exe
exp2.exe

flex sdd.l
gcc lex.yy.c -o sdd.exe
sdd.exe
```

### Flex + Bison/YACC experiments
```bat
win_bison -d expr.y
win_flex expr.l
gcc expr.tab.c lex.yy.c -o expr.exe
expr.exe

win_bison -d type.y
win_flex type.l
gcc type.tab.c lex.yy.c -o type.exe
type.exe

win_bison -d ex4.y
win_flex ex4.l
gcc ex4.tab.c lex.yy.c -o ex4.exe
ex4.exe
```

### C-only experiments
```bat
gcc exp4.c -o exp4.exe
exp4.exe

gcc opt.c -o opt.exe
opt.exe

gcc opt10.c -o opt10.exe
opt10.exe
```

## Exam checklist
1. Open Command Prompt.
2. `cd` into the correct experiment folder.
3. Create the source file(s) with the **same filenames used by the commands/includes**.
4. For LEX-only: `flex` → `gcc` → run.
5. For LEX + YACC: `win_bison -d` → `win_flex` → `gcc` → run.
6. For C-only: `gcc` → run.
7. Enter the test input and verify the expected output from the lab sheet.
8. Do not rename generated header/source files unless you also update the source code; especially note `expr.tab.h`, `type.tab.h`, and `ex4.tab.h`.

## Source fidelity notes
- EXP1–EXP7, EXP9 and EXP10 code is taken from the supplied DOCX files.
- EXP8 is intentionally marked as incomplete because the supplied document does not contain the calculator program.
- EXP9 is intentionally preserved as supplied rather than silently fixing code that appears malformed in the DOCX.

---
Generated as an exam/GitHub execution reference from the uploaded practical documents.