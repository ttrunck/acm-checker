#Workflow

Voyons une manière de s'organiser afin de travailler
efficacement. Nous illustrons notre exemple en résolvant le problème
suivant:

> Étant une liste de prénoms composés uniquement de \[a-zA-Z\], écrivez
> pour chaque prénom, **Bonjour _prénom_ !**

Commençons par créer le fichier contenant un exemple d'entrée dans un
fichier `hello.in`:

~~~
Luke
Leia
Han
Chewbacca
R2D2
~~~

Puis le fichier contenant la sortie attendue `hello.res`:

~~~
Bonjour Luke !
Bonjour Leia !
Bonjour Han !
Bonjour Chewbacca !
Bonjour R2D2 !
~~~

Nous pouvons maintenant commencer à coder dans un fichier `hello.cpp`:

~~~ cpp
#include<iostream>
#include<string>

using namespace std;
		
int main(){
	string s;
	while(cin >> s)
		cout << "Bonjour " << s << " !\n";
}
~~~

Quelques remarques sur ce code:

* `#include<iostream>` permet d'utiliser `cin` et `cout` afin de lire l'entrée et écrire la sortie.
* `#include<string>` permet d'utiliser les `string`
* `using namespace std` permet d'éviter de devoir préfixer les objets
  de la librairie standard de `std::`. Vu que les programmes d'ACM sont
  courts et ne peuvent rien inclure hors de la STL cela ne pose pas de
  problème.

Il faut ensuite compiler ce programme. À l'école le compilateur est
`g++`. Il est fortement conseiller d'*activer les warnings* et on va
également utiliser certaines extensions de C++11 vous compilez donc
avec `g++ -Wall -Wextra -std=c++11 hello.cpp` qui vous produit un exécutable
`a.out`.

Vous pouvez maintenant tester votre programme. Comme il lit sur
l'entrée standard il faut rediriger le contenu de votre fichier
d'entrée sur l'entrée standard `./a.out < hello.in`. La sortie est
alors affichée sur la sortie standard, vous pouvez alors regarder si
le programme vous semble correct.

Attention votre sortie doit être exactement la même que la sortie
attendue, vous pouvez donc utiliser `diff` pour vérifier que votre
sortie est exactement la bonne. `./a.out < hello.in > hello.out`
permet de rediriger la sortie dans votre programme dans le fichier
`hello.out`. Ensuite la commande `diff hello.out hello.res` vous
permet de vous assurer que les deux fichiers sont identiques et vous
affiche les différences dans le cas contraire.

Enfin votre programme doit s'exécuter assez rapidement, si vous avez
un jeu de données sensiblement de même taille que celui attendu vous
pouvez regarder le temps que met votre programme à traiter ce jeu de
données. Pour cela compilez avec les optimisations `g++ -O2 -Wall
-Wextra -std=c++11 hello.cpp`, puis vous pouvez utiliser
`time ./a.out < hello.in > hello.out`. Si votre programme est trop
lent vous pouvez le stopper en faisant `Ctrl + c`.

#Entrée Sortie

Dans chaque problème il faudra lire sur l'entrée standard et écrire
sur la sortie standard. Certains problèmes ne sont pas spécialement
pensés pour faciliter la lecture d'entrée, heureusement C++ dispose de
plusieurs fonctions qui vous faciliteront la partie entrée sortie. Nous
vous conseillons fortement d'utiliser les fonctions `cin/cout` du C++
plutôt que `scanf/printf` de C.

##Généralités

 Les fonctions les plus utilisées seront `>>` et `<<`. `getline` pourra
 parfois nous être utile. Il est possible de mettre `>>` et `getline`
 dans la condition d'une boucle `while` pour lire entièrement un
 flux de taille arbitraire.

Dans les cas simples et fréquents, `>>` lit un stream et interprète les
données. Il saute les caractères blancs et s'arrête à la fin du
flux. Attention la position dans le flux est juste après le mot lu.

`getline` lit une ligne entière. Attention en le combinant avec `<<` on
peut facilement lire uniquement le caractère de retour de ligne au
lieu de la ligne suivante. Dans ce cas on peut utiliser `ignore` pour
extraire sans traiter les caractères blancs.

Voici un exemple sur ce fichier d'entrée. (3.23e3 est le nombre 3230
en notation scientifique)

~~~
12      3.23e3

Coucou@&#.       p
Paf c
c
Hello
World !
~~~



~~~ cpp
#include<iostream>
#include<string>

using namespace std;

int main(){
int n;
double d;
string s;
char c;
cin >> n >> d >> s >> c;
//n=12, d=3230, s=Coucou@&#., c=p
cin.ignore();
getline(cin, s);
cin >> c;
//s="Paf c";
//c='c'
getline(cin, s);
//s="\n"
cin >> s;
//s=="Hello"
cin.ignore();
getline(cin, s);
//s="World !"
}
~~~

##Lire caractère par caractère

Par défaut `cin` saute les caractères blancs (espace tabulation et
retour de ligne), si vous voulez vraiment lire caractère par caractère
il faut passer le manipulateur `noskipws`. Pour revenir dans l'état
normal il faut ensuite passer le manipulateur `skipws`. Voici un exemple:

~~~ cpp
#include<iostream>
#include<sstream>

using namespace std;

int main(){
  char a, b, c;

  {	
  istringstream iss ("  123");
  iss >> skipws >> a >> b >> c;
  cout << a << b << c << '\n';
  }
  {
  istringstream iss ("  123");
  iss >> noskipws >> a >> b >> c;
  cout << a << b << c << '\n';
  }
}
~~~

qui affiche

~~~
123
  1
~~~

##Parser une ligne contenant un nombre arbitraire d'entiers

Nous avons vu qu'il était facile de traiter un fichier avec un nombre
arbitraire de lignes. Voyons comment adapter ceci pour traiter une ligne
avec un nombre arbitraire d'entiers à traiter.

Nous allons lire toute la ligne d'un coup avec `getline`, et
transformer la chaine obtenue en flux afin de pouvoir utiliser la même
méthode que pour lire le fichier.

~~~ cpp
#include<iostream>
#include<string>
#include<sstream>

using namespace std;

int main(){
	string s;
	//commençons par lire la ligne
	getline(cin, s);
	//transformons ensuite notre chaine de caractères en un flux de lecture
	istringstream ss(s);
	//nous pouvons maintenant utiliser ss comme cin
	int tmp;
	while(ss >> tmp)
		cout << tmp;
}
~~~

##Afficher exactement 6 chiffres après la virgule

Rien de compliqué, mais il faut le savoir, le manipulateur
`setprecision(n)` permet d'afficher les nombres à virgule avec une
précision de `n`. Le manipulateur `fixed` permet lui de forcer
l'affichage des 0 non significatifs.

~~~ cpp
#include<iostream>
#include<iomanip>

using namespace std;

int main(){
	double a=1.2, b=1.23456789123456789;
	cout << a << ' ' << b << '\n';
	cout << fixed << setprecision(6) << a << ' ' << b << '\n';
}
~~~

affiche

~~~
1.2 1.23457
1.200000 1.234568
~~~

##Mes entrées sorties sont trop lentes

C'est un problème assez délicat qui dépend pas mal de la plateforme
(cygwin sous windows est extrêmement mauvais par exemple), mais en
général il est dit que `scanf/printf` sont plus rapides que
`cin/cout`. En fait par défaut `cin/cout` sont synchronisés sur
`scanf/printf` ce qui explique leurs lenteurs. Si vous n'utilisez pas
`scanf/printf` cette synchronisation est inutile, vous pouvez la
désactiver. De plus votre programme étant testé en mode non
interactif, vous n'avez pas non plus besoin de la synchronisation entre
`cin` et `cout`. Sans cette synchronisation (et ça dépend des
plateformes et du compilateur) `cin/cout` sont globalement aussi
rapides que `scanf/printf`. Notez que cette optimisation n'est utile
que lorsque les entrées sorties ne sont pas négligeables devant la
complexité de votre algorithme (i.e. votre algo est au pire en O(n
ln(n))).

Pour désactiver ces synchronisations il suffit d'utiliser
`sync_with_stdio` et `.tin`, voici un exemple.

~~~ cpp
#include<iostream>

using namespace std;

int main(){
	ios::sync_with_stdio(false);
	cin.tie(nullptr);
	int tmp;
	while(cin >> tmp)
		cout << tmp << '\n';
}
~~~

#Nombres

Pour représenter les nombres, vous pouvez vous limiter à trois types:

* `double` dès que les nombres considérés ne sont pas entiers, utilisez
  des doubles.
* `int` pour les entiers inférieurs à 10^9^.
* `long long` pour les entiers jusqu'à 10^18^.

Attention utiliser des `int` à la place de `long long` est
sensiblement plus rapide (i.e. certains problèmes ne valideront pas
avec des `long long`).

Attention également aux calculs intermédiaires qui ne doivent pas
dépasser la capacité du type. Par exemple si on prend cet extrait de
code d'une exponentiation modulaire rapide avec `a`,  `tmp` et `MOD`
des entiers inférieurs à 10^9^

~~~ cpp
long long a,tmp,MOD;
return (a*tmp*tmp)%MOD;
~~~

Donne un résultat faux, en effet le produit `a*tmp*tmp` peut dépasser
10^18^ même si on en prend ensuite le modulo, il faut donc prendre le
modulo des résultats intermédiaires.

~~~ cpp
int a,tmp,MOD;
return (a*((tmp*tmp)%MOD))%MOD;
~~~

Donne également un résultat incorrect, car les produits intermédiaires
dépassent la capacité d'un `int`.

~~~ cpp
long long a,tmp,MOD;
return (a*((tmp*tmp)%MOD))%MOD;
~~~

est la version correcte.

#Vector

Vector est un conteneur pour les tableaux dynamiques. Les fonctions
les plus utiles sont l'opérateur `[]` et la fonction
`push_back`. `push_back` permet d'insérer en fin de tableau une nouvelle
valeur, cela agrandit le tableau en temps constant amorti. L'opérateur
`[]` permet d'accéder à la n^ème^ valeur du tableau en temps
constant. Les indices commencent à partir de 0 jusqu'à la taille du
vector-1. Le tri de tableau existe dans la bibliothèque de template
`algorithm`.

Voici un exemple simple d'utilisation.

~~~ cpp
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

int main(){
	//crée un tableau t de 5 entiers (1,2,3,4,5)
	vector<int> t{1,2,3,4,5};
	//crée un tableau d de 12 flotants initialisés à 3.14
	vector<double> d(12,3.14);

	cout << d[0] << '\n';
	//crée un tableau p de char de taille 0
	vector<char> p;

	p.push_back('z');
	//p est maintenant de taille 1 et contient 'z';
	p.push_back('a');
	//p est maintenant de taille 2 et contient 'z' puis 'a';

	//affiche le tableau p
	for(int i=0;i<int(p.size());++i)
		cout << p[i] << ' ';
	cout << '\n';

	sort(begin(p), end(p));
	//p est maintenant de taille 2 et contient 'a' puis 'z';

	//une autre manière d'afficher le tableau p
	for(char c:p)
		cout << c << ' ';
	cout << '\n';
}
~~~

Qui affiche:

~~~
3.14
z a
a z
~~~

#Map

Map est un conteneur permettant d'utiliser des tableaux associatifs
triés par clé supportant des opérations en O(lg(n)). L'opérateur le
plus utile est `[key]` qui permet d'accéder à la valeur stockée pour
key. Attention si cette valeur n'existe pas elle est crée avec
l'initialisateur par défaut (pour les `int` c'est 0).

Vous pouvez voir les autres fonctions utiles des `map` [ici](http://www.cplusplus.com/reference/map/map/)

Voici un exemple simple d'utilisation.

~~~ cpp
#include<iostream>
#include<map>
#include<string>

using namespace std;

int main(){
  map<char,string> mymap;

  mymap['b']="another element";
  mymap['a']="an element";
  mymap['c']=mymap['b'];

  cout << "mymap['a'] is " << mymap['a'] << '\n';
  cout << "mymap['b'] is " << mymap['b'] << '\n';
  cout << "mymap['c'] is " << mymap['c'] << '\n';
  cout << "mymap['d'] is " << mymap['d'] << '\n';

  //version pre c++11
  //for(map<char,string>::iterator it=mymap.begin();it!=mymap.end();++it)
  //  cout << "key: " << it->first << ", value: " << it->second << '\n';

  for(auto& x:mymap)
	cout << "key: " << x.first << ", value: " << x.second << '\n';

}
~~~

qui affiche:

~~~
mymap['a'] is an element
mymap['b'] is another element
mymap['c'] is another element
mymap['d'] is 
key: a, value: an element
key: b, value: another element
key: c, value: another element
key: d, value: 
~~~

#Mes propres structures de données

Supposons que vous vouliez une structure de donnée pour représenter un
cercle. Vous voulez également pouvoir utiliser des `vector<cercle>`
ainsi que d'autres conteneurs de cercles. Il faut pour cela créer votre
propre structure de données. Le mot clé est struct et il faut également
définir un constructeur (une fonction membre sans type de retour du
même nom que votre struct). Attention il y a un `;` à la fin de la
déclaration. Voici un exemple:

~~~ cpp
#include<iostream>
#include<vector>
#include<algorithm>

using namespace std;

struct mycercle{
  int x,y,r; 

 //notez le c=1, qui rend le troisième paramètre optionnel
 //s'il n'est pas spécifié ce sera 1
 mycercle(int a, int b, int c=1){
    x=a;
    y=b;
    r=c;
  }
  

  //Une déclaration équivalente
  //mycercle(int a, int b, int c=1):x(a),y(b),r(c){}

  //on peut aussi spécifier un opérateur de comparaison afin
  //d'utiliser les algorithmes de la STL

  //que se passe t'il si je compare mon cercle avec un autre cercle c
  //noter les const qui permettent de s'assurer qu'aucun cercle ne sera
  //modifié durant la comparaison
  //ici un cercle est plus petit qu'un autre s'il a plus petit rayon.
  bool operator<(const mycercle& c) const{
    return r<c.r;
  }

};

//le mot clé const permet de s'assurer que c ne sera pas modifié
//le & permet de passer c par référence
void affiche(const mycercle& c){
  cout << "Le cercle a pour centre " << c.x << ','
       << c.y << " et pour rayon " << c.r <<'\n';
} 

int main(){
  mycercle C1(0,0,2);
  affiche(C1);
//Le cercle a pour centre 0,0 et pour rayon 2
  mycercle C2(2,7);
  affiche(C2);
//Le cercle a pour centre 2,7 et pour rayon 0
  vector<mycercle> t1;
  //vector<mycercle> t2(5);
  //Ne fonctionne pas, car il n'y a pas de constructeur par défaut
  //il faut au choix donner des valeurs par défaut à tous les paramètres
  //du constructeur ou faire un constructeur sans paramètres

  vector<mycercle> t3{mycercle{1,2,3}, mycercle{4,5,6}};
  sort(begin(t3), end(t3));
}
~~~