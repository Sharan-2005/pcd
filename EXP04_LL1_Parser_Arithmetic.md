# Experiment 4 — LL(1) Parser for Arithmetic Expressions

## Aim
Design and implement an **LL(1) parser** for validating arithmetic expressions.

## File required
Create:

```text
ll1.c
```

## Step 1 — Create the C source file
Create `ll1.c` and paste the supplied code exactly as provided:

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

## Step 2 — Open Command Prompt in the file location

```cmd
cd C:\Your\Experiment\Folder
```

## Step 3 — Compile with GCC

```cmd
gcc ll1.c -o ll1.exe
```

## Step 4 — Run

```cmd
./ll1.exe
```

## Step 5 — Test valid input
Use:

```text
i+i*i
```

Expected:

```text
String Accepted
```

Another supplied valid example:

```text
(i+i)*i
```

Expected:

```text
String Accepted
```

## Step 6 — Test invalid input
Enter an invalid arithmetic expression, for example an expression that does not follow the parser's grammar. The program should display:

```text
String Rejected
```

## Quick exam commands

```cmd
gcc ll1.c -o ll1.exe
ll1.exe
```

## Result
The LL(1) parser checks the input expression and reports whether the string is accepted or rejected.
