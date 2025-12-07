#include <stdio.h>
#include <stdlib.h>

typedef long long ll;

typedef struct
{
 ll r_prev;
 ll r;
 ll q;
 ll rem;
} Step;

int euclid_steps(ll a, ll b, Step **out_steps, int *out_n)
{
 if (b == 0)
 {
  *out_steps = NULL;
  *out_n = 0;
  return 0;
 }

 int cap = 32;
 Step *steps = malloc(cap * sizeof(Step));
 if (!steps)
 {
  perror("malloc");
  exit(1);
 }

 ll r_prev = a;
 ll r = b;
 int n = 0;

 printf("\n1) Divisions euclidiennes (affichage étape par étape) :\n");
 while (r != 0)
 {
  ll q = r_prev / r;
  ll rem = r_prev - q * r;
  if (n >= cap)
  {
   cap *= 2;
   steps = realloc(steps, cap * sizeof(Step));
   if (!steps)
   {
    perror("realloc");
    exit(1);
   }
  }
  steps[n].r_prev = r_prev;
  steps[n].r = r;
  steps[n].q = q;
  steps[n].rem = rem;
  printf("%lld = %lld * %lld + %lld\n", r_prev, r, q, rem);

  r_prev = r;
  r = rem;
  n++;
 }

 printf("PGCD(%lld, %lld) = %lld\n\n", a, b, r_prev);

 *out_steps = steps;
 *out_n = n;
 return (int)r_prev;
}

void bezout_from_steps(Step *steps, int n, ll a, ll b, ll *out_u, ll *out_v)
{
 if (n == 0)
 {
  *out_u = 1;
  *out_v = 0;
  printf("Cas spécial : b = 0 => PGCD = %lld = 1*%lld + 0*%lld\n", a, a, b);
  return;
 }

 int m = n - 1;
 ll gcd = steps[m].r_prev;

 ll u_prev = 1, v_prev = 0;
 ll u_curr = 0, v_curr = 1;

 printf("2) La remonté de l'algorithme d'euclide  pour obtenir les coefficients de Bézout :\n\n");

 for (int j = 0; j < n; ++j)
 {
  ll q = steps[j].q;
  ll rem = steps[j].rem;
  ll u_next = u_prev - q * u_curr;
  ll v_next = v_prev - q * v_curr;

  printf("Remontee de %d\n: %lld = %lld - (%lld) * %lld  => ", steps[j].rem, steps[j].rem, steps[j].r_prev, q, steps[j].r);
  printf("%lld*(%lld) + %lld*(%lld)\n", u_next, a, v_next, b);

  u_prev = u_curr;
  v_prev = v_curr;
  u_curr = u_next;
  v_curr = v_next;
 }

 *out_u = u_prev;
 *out_v = v_prev;

 printf("\n=> Coefficients trouvés : \n u=%lld et v=%lld \n =>(%lld)*%lld + (%lld)*%lld = %lld  ",*out_u,*out_v,
      *out_u, a, *out_v, b,gcd);
}

void find_small_solution(ll a, ll b, ll d, ll x0, ll y0, ll *x_small, ll *y_small)
{
 ll mod = llabs(b / d);
 if (mod == 0)
 {
  *x_small = x0;
  *y_small = y0;
  return;
 }
 ll xm = ((x0 % mod) + mod) % mod;
 ll k = (xm - x0) / (b / d);
 ll ys = y0 - (a / d) * k;
 *x_small = xm;
 *y_small = ys;
}

int main(void)
{
 ll a, b, c;
 printf("Resolution de l'equation ax + by = c\n");
 printf("Entrez a: ");
 if (scanf("%lld", &a) != 1)
 {
  fprintf(stderr, "Lecture a fail\n");
  return 1;
 }
 printf("Entrez b: ");
 if (scanf("%lld", &b) != 1)
 {
  fprintf(stderr, "Lecture b fail\n");
  return 1;
 }
 printf("Entrez c: ");
 if (scanf("%lld", &c) != 1)
 {
  fprintf(stderr, "Lecture c fail\n");
  return 1;
 }

 Step *steps = NULL;
 int n = 0;
 ll d = euclid_steps(a, b, &steps, &n);
 if (d == 0 && n == 0)
 {
  printf("a = %lld et b = %lld => PGCD non défini (les deux nuls). Terminé.\n", a, b);
  free(steps);
  return 0;
 }

 ll u, v;
 bezout_from_steps(steps, n, a, b, &u, &v);

 printf("\n\n3) Vérification de l'existence d'une solution pour c = %lld :\n", c);
 if (c % d != 0)
 {
  printf("PGCD(a,b) = %lld ne divise pas %lld => aucune solution.\n", d, c);
  free(steps);
  return 0;
 }
 else
 {
  printf("PGCD(a,b) = %lld divise %lld\n => il existe des solutions.\n", d, c);
 }

 ll factor = c / d;
 ll x0 = u * factor;
 ll y0 = v * factor;
 printf("\n4) Solution particulière calculé :\n\n");
 printf("On a (%lld)*(%lld) + (%lld)*(%lld) = %lld\n", a, u, b, v, d);
 int mul;
 mul = c / d;
 printf("Multiplier par %d  donne :\n",mul); 
 printf("(%lld)*(%lld) + (%lld)*(%lld) = %lld\n", a, u*mul, b, v*mul, d);
 printf("\n => x0 = %lld  ,  y0 = %lld\n", x0, y0);
 printf("Verification numerique : \n%lld*%lld + %lld*%lld = %lld\n", a, x0, b, y0, a * x0 + b * y0);

 ll x_small, y_small;
 find_small_solution(a, b, d, x0, y0, &x_small, &y_small);
 printf("\n l'un du solution particlier trouver : \n");
 printf(" =>x = %lld , y = %lld\n", x_small, y_small);
 printf("Verification : %lld*%lld + %lld*%lld = %lld\n", a, x_small, b, y_small, a * x_small + b * y_small);

 printf("\n5) Solution générale (paramètre k entier) :\n");
 printf(" x = x0 + (b/d)*k \n=> x= %lld + (%lld)*k\n", x0, b / d);
 printf(" y = y0 - (a/d)*k \n=> y= %lld - (%lld)*k\n", y0, a / d);

 free(steps);
 return 0;
}
