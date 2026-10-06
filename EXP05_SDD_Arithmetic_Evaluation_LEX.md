# Experiment 5 — SDD for Evaluating Arithmetic Expressions Using LEX

## Aim
Design and implement a **Syntax-Directed Definition (SDD)** for evaluating arithmetic expressions using LEX.

## File required
Create:

```text
sdd.l
```

## Step 1 — Review the supplied grammar
The supplied experiment uses:

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

## Step 2 — Create `sdd.l`
Save the following supplied program as `sdd.l` without changing the code:

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

## Step 3 — Open Command Prompt in the experiment folder

```cmd
cd C:\Your\Experiment\Folder
```

## Step 4 — Generate the lexer

```cmd
flex sdd.l
```

This creates:

```text
lex.yy.c
```

## Step 5 — Compile

```cmd
gcc lex.yy.c -o sdd.exe
```

## Step 6 — Run

```cmd
sdd.exe
```

## Step 7 — Enter an arithmetic expression
Try an expression such as:

```text
2+3*4
```

Press **Enter**. The program prints the calculated result.

## Other useful tests

```text
10-2*3
(5+3)*2
20/4
```

## Quick exam commands

```cmd
flex sdd.l
gcc lex.yy.c -o sdd.exe
sdd.exe
```

## Result
The LEX program evaluates arithmetic expressions using the precedence rules implemented in the supplied code and prints the result.
