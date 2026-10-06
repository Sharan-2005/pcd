# Experiment 1 — Finite Automata for Regular Languages

## Aim
Implement a finite automaton for strings over **{a,b}** that start and end with the letter **a**.

## Files required
Create one file:

```text
automata.l
```

## Step 1 — Open the experiment folder
Open Command Prompt and move to the folder where `automata.l` will be saved.

```cmd
cd C:\Your\Experiment\Folder
```

Replace the path with your actual folder path.

## Step 2 — Create `automata.l`
Create a file named `automata.l` and paste the supplied code below **without changing it**.

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

## Step 3 — Generate the lexer
Run:

```cmd
flex automata.l
```

This generates:

```text
lex.yy.c
```

## Step 4 — Compile with GCC

```cmd
gcc lex.yy.c -o automata.exe
```

## Step 5 — Run

```cmd
automata.exe
```

## Step 6 — Test
Try these one at a time:

```text
aba
abbbba
ab
baba
```

Expected results from the supplied experiment:

| Input | Result |
|---|---|
| `aba` | STRINGS ACCEPTED |
| `abbbba` | STRINGS ACCEPTED |
| `ab` | STRING REJECTED |
| `baba` | STRING REJECTED |

## Quick exam commands

```cmd
flex automata.l
gcc lex.yy.c -o automata.exe
automata.exe
```

## Result
The finite automaton program is generated, compiled, executed, and tested for strings beginning and ending with `a`.
