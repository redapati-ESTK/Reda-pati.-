# Reda-pati.-
#include <stdio.h>
int main() {
    int a, b, r, q;
    int etape = 1;
    printf("Entrez le premier nombre : ");
    scanf("%d", &a);
printf("Entrez le premier nombre : ");
scanf("%d",&b);
    printf("\n--- Début du calcul du PGCD ---\n");
    while (b != 0) {
        r = (a % b);
        q = (a / b);
        printf("Étape %d : a = %d, b = %d, (a / b) = %d,le reste = %d\n", etape, a, b, q, r);
        a = b;
        b = r;
        etape++;
    }
    printf("--- Fin du calcul ---\n");
    if (a!=1)
    printf("Le PGCD est : %d\n", a);
else 
printf(" a et b sont premiers entre eux ");
    return 0;
}
