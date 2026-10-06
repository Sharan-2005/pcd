# Experiment 9 — Simple Code Optimization Techniques

## Aim
Implement simple code optimization techniques. The supplied experiment title mentions **constant folding, strength reduction and algebraic transformation**.

## Important source note
The supplied program section contains intermediate-code processing including dead-code elimination and common-expression handling. The source is reproduced below without correction or modification.

## File required

```text
optimization.c
```

## Step 1 — Create the C file
Create `optimization.c` and paste the supplied program exactly:

```c
#include <stdio.h>
#include <string.h>

struct op
{
    char l;
    char r[20];
} op[10], pr[10];

int main()
{
    int a, i, k, j, n, z = 0, m, q;
    char *p, *l;
    char temp, t;
    char *tem;

    printf("Enter the Number of Values: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++)
    {
        printf("left: ");
        scanf(" %c", &op[i].l);

        printf("right: ");
        scanf("%19s", op[i].r);
    }

    printf("\nIntermediate Code\n");

    for (i = 0; i < n; i++)
    {
        printf("%c = ", op[i].l);
        printf("%s\n", op[i].r);
    }

    /* Dead Code Elimination */
    for (i = 0; i < n - 1; i++)
    {
        temp = op[i].l;

        for (j = 0; j < n; j++)
        {
            p = strchr(op[j].r, temp);

            if (p)
            {
                pr[z].l = op[i].l;
                strcpy(pr[z].r, op[i].r);
                z++;
            }
        }
    }

    pr[z].l = op[n - 1].l;
    strcpy(pr[z].r, op[n - 1].r);
    z++;

    printf("\nAfter Dead Code Elimination\n");

    for (k = 0; k < z; k++)
    {
        printf("%c = ", pr[k].l);
        printf("%s\n", pr[k].r);
    }

    /* Common Expression Elimination */
    for (m = 0; m < z; m++)
    {
        tem = pr[m].r;

        for (j = m + 1; j < z; j++)
        {
            p = strstr(tem, pr[j].r);

            if (p)
            {
                t = pr[j].l;
                pr[j].l = pr[m].l;

                for (i = 0; i < z; i++)
                {
                    l = strchr(pr[i].r, t);

                    if (l)
                    {
                        a = l - pr[i].r;
                        printf("pos: %d\n", a);
                        pr[i].r[a] = pr[m].l;
                    }
                }
            }
        }
    }

    printf("\nEliminate Common Expression\n");

    for (i = 0; i < z; i++)
    {
        printf("%c = ", pr[i].l);
        printf("%s\n", pr[i].r);
    }

    /* Remove duplicate expressions */
    for (i = 0; i < z; i++)
    {
        for (j = i + 1; j < z; j++)
        {
            q = strcmp(pr[i].r, pr[j].r);

            if ((pr[i].l == pr[j].l) && !q)
            {
                pr[i].l = '\0';
            }
        }
    }

    printf("\nOptimized Code\n");

    for (i = 0; i < z; i++)
    {
        if (pr[i].l != '\0')
        {
            printf("%c = ", pr[i].l);
            printf("%s\n", pr[i].r);
        }
    }

    return 0;
}
```

## Step 2 — Open Command Prompt

```cmd
cd C:\Your\Experiment\Folder
```

## Step 3 — Compile

```cmd
gcc optimization.c -o optimization.exe
```

## Step 4 — Run

```cmd
./optimization.exe
```

## Step 5 — Enter the number of values
The program first asks:

```text
Enter the Number of Values:
```

Enter the number of intermediate-code statements you want to provide.

## Step 6 — Enter each statement
For every statement, enter:

```text
left: <single-character variable>
right: <right-hand side text>
```

Repeat until the requested number of values is entered.

## Step 7 — Observe the optimization stages
The supplied program prints stages including:

```text
Intermediate Code
After Dead Code Elimination
Eliminate Common Expression
Optimized Code
```

## Quick exam commands

```cmd
gcc optimization.c -o optimization.exe
./optimization.exe
```

## Result
The supplied program accepts intermediate-code statements and prints the intermediate, reduced, and optimized forms according to the supplied implementation.
