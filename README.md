# Reda-pati.-
#include <stdio.h>

int main() {
    int a, b, r;

    // Lecture des deux nombres
    printf("Entrez deux entiers positifs : ");
    scanf("%d %d", &a, &b);

    // Algorithme d'Euclide
    while (b != 0) {
        r = a % b;  // reste de la division
        a = b;
        b = r;
    }

    // Affichage du résultat
    printf("Le PGCD est : %d\n", a);

    return 0;
}
