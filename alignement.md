## Alignement de séquences
### Introduction
l’alignement de séquences concerne au minimum deux séquences. Un alignement est une écriture de deux (ou plus) séquences, l’une sous l’autre de façon à faire apparaître des identités (ou des similitudes de séquences). A chaque alignement correspond un score id % qui peut être calculé comme le pourcentage d’identité (nombre d’identité/longueur de l’alignement) lors de l’édition de la séquence.
La distance d’édition de deux séquences est le nombre minimal d’opérations à effectuer pour transformer l’une dans l’autre.

Soit les deux séquences suivantes :
```
Sequence 1 A G V S I L N Y A
Sequence 2 V S I L Y A K R
```
L’écriture des deux séquences donne : Identité = 0 ; longueur = 9 ; id % = 0.

l’alignement de séquences nécessite une police de caractères non proportionnelle (courrier par exemple).

Si on admet que la séquence 2 a perdu les acides aminés N-terminaux au cours de l’évolution, l’alignement devient : 

```
Sequence 1 A G V S I L N Y A -
Sequence 2 - - V S I L Y A K R
               * * * *
```

L'alignement donne : Identité = 4 ; longueur = 10 ; id% = 40.

Cela revient à faire glisser la séquence 2 de deux acides aminé sur la droite. L'opération autorisée est un coulissement de séquence. On pourrait aussi envisager un coulissement de trois lettres vers la droite qui donnerait l'alignement suivant :

```
Sequence 1 A G V S I L N Y A -
Sequence 2 - - - V S I L Y A K R
                         * *
```
Le nouvelle alignement donne : Identité = 6 ; longueur = 11 ; id% = 18.

Trouver le meilleur alignement par glissement est trivial (infomatiquement parlant).

En pratique l'évolution a pu faire disparaître (deletion) ou apparaître (insertion) des acides aminés à l'intérieur des séquences. Une délétion dans une séquence correspond à une insertion dans l'autre (on parle alors d'"**indel**"). Cela revient à faire des "trous" (gaps) dans les séquences comme dans l'exemple suivant dans lequel une délétion dans la séquence 2 est apparue en position 5.

```
Sequence 1 A G V S I L N Y A - -
Sequence 2 - - V S I L - Y A K R
               * * * *   * *
```
L'alignement donne : Identité = 6 ; longueur = 11 ; id% = 55.

A ce stade, la question qui se pose est : "quel est le meilleur alignement?" Le dernier possède le plus fort taux d'identité mais il a fallu pour cela créer un gap interne (faire l'hypothèse d'un événement évolutif moins probable qu'une simple substitution). Il est donc logique de pénaliser le score de l'alignement obtenu pour la création d'indel. On pourrait par exemple diminuer le score de six identités d'une valeur égale au nombre de gap interne (ici 1), ce qui donnerait 5/11, soit un score = 45 qui reste le meilleur score. A ce stade, on voit bien que le score de la pénalité va influencer l'alignement final. Si on avait choisi de deux unités chaque gap interne, le score aurait été 4/11, soit 36, et l'alignement à quatre identités serait devenu le meilleur. Ainsi, plusieurs systèmes de pénalités pour les gaps ont été imaginés. Tout d'abird, la suppression d'un segment peut correspondre à un seul événement évolutif qui concerne la totalité su segment. Ainsi, le système de pénalité doit prendre en compte la longueur de l'indel (un indel long doit être plus pénalisé qu'un indel court). Par ailleurs, il a pu se produire plusieurs événements évolutifs distincts dans une région (même si le nombre total de gaps reste faible); mais l'apparition d'un indel doit être plus pénalisante que le prolongement d'un indel déjà présent. D'où la pénalité souvent représentée par une fonctions affine :

```
P= x + y*L*
```

où `x` est pénalité fixe pour la création d'un indel (ouverture de gap) et `y*L*` est la pénalité pour un gap de la longueur *L*. Enfin, les événements évolutifs ont tendance à se produire dans les régions externes des protéines, les plus exposées au solvant, les moins structurées. Ainsi, on peut imaginer des systèmes de pénalité qui favorisent les indels dans ces régions. L nombre d'alignement possibles entre deux séquences en autorisant des indels peut dépasser le nombre d'atomes dans l'Univers (en fonction des longueurs).

Tout comme la recherche dans les banques, l'alignement peut être global ou local.

Par exemple, l'alignement suivant présente sept identités :
```
G G C T G A C C A C C - T T
|     |   | |     | |   |
G A - T C A C T T C C A T G
```

Ainsi, le premier alignement est celui qui maximise les identités sur la totalité des deux séquences. On parle alors d'alignement global. L'application majeure de ce type d'alignement de séquences homologues en vue d'identifier des motifs (génes conservés) par l'évolution. Au cours de l'évolution, les séquences varient de façon à préserver (voire optimiser) la fonction biologique.

Un alignement local des mêmes séquences fournira aussi sept identités mais donnera un alignement different :

```
G G C T G A C C A C C T T
        | |   | | |   | |
        G A T C A C - T T C C A T G 
```

Cet alignement local sera privilégié si le plus long chevauchement entre deux séquences est recherché comme dans le cas de la reconstruction à partir de données obténues par séquençage. Le choix de l'alignement global ou local revient donc à l'utilisateur biologiste en fonction des objectifs poursuivis.

### Comapraison de protéine homologues (algotithme global)

Il s'agit d'un algorithme de programmation dynamique pour l'alignement global optimal entre deux séquences. Les trois parametre du programme sont le **score** pour i) l'**identité**, ii) la substitution et iii) l'indel. Les deux séquences sont placées dans un tableau. Le principe consiste à calculer des scores de chaque case de tableau en partant de la case (0,0) jusqu'à la case (n,m) en remplissant ligne par ligne en simulant les trois types d'opération possibles (insertion, délétion ou mise en correspondances). Dans le cas de la mise en correspondance, on peut avoir subsitution de Ai par Bj ou identité Ai, Aj. Pour chaque cas le score S(i,j) de la case i,j est calculé des trois façons symbolisant les trois déplacement possibles :

* [Alignement avec MegaX](https://github.com/Ezechiel-Tibiri/Cours_bioinformatique_2020/blob/main/data.zip)
* Télécharger le nouveau fichier pour l'alignement en utilisant l'option MUSCLE [ici](https://github.com/Ezechiel-Tibiri/Cours_bioinformatique_2020/blob/main/data_new.zip), puis exporter le resultat d'alignement sous un nouveau nom (**aln1_SPFMV.fasta**).
