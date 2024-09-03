# Notions d’UML

UML (Unified Modeling Language) est un langage de modélisation pour les sytèmes
logiciels orientés objet. Ce chapitre résume les principaux éléments,
les principales relations et les principaux diagrammes définis par UML.

#### NOTE
UML est un langage de modélisation complet et complexe. Ce chapitre ne se
limite donc qu’à une découverte d’UML.

## Les éléments

### La classe

On représente une classe par un rectangle avec son nom.

### L’objet

On représente un objet en donnant son nom (optionnel) suivi de **:** et de la classe
de l’objet. Le tout doit être souligné.

### Les attributs

Les attributs d’une classe sont représentés sous la forme de liste dans
un cartouche sous le nom de la classe.

En UML, le type est défini après le nom et séparé par **:**.

### Les opérations

Les opérations d’une classe sont représentées sous la forme
d’une liste dans un cartouche après les attributs (s’il y en a). Une opération
peut être définie par son nom, ses paramètres et son type de retour (séparé par **:**).

#### NOTE
UML utilise le terme *opération* mais dans la conception orientée objet, on utilise
généralement le terme *méthode*.

### Attributs et opérations de classe

Les attributs et opérations de classe sont soulignés.

### La portée

La portée des attributs et des opérations est représentée par **+** pour publique,
**-** pour privée, **#** pour protégée, **~** pour paquet (*package*) et **/**
pour indiquer un attribut dérivé.

### La responsabilité

La responsabilité d’une classe peut être décrite dans un dernier cartouche.

### Le stéréotype

Un ou des stéréotypes peuvent être ajoutés à des éléments UML pour apporter
une précision. Ce mécanisme est utilisé pour enrichir UML.

Dans l’exemple ci-dessus, on utilise un stéréotype « persistant » pour indiquer que
des objets de cette classe peuvent être sauvegardés par le système.

### Classe et opération abstraite

Une classe ou une opération abstraite est représentée en écrivant son nom
en italique.

### L’interface

Une interface est représentée par un cercle. On peut également la représenter
comme une classe avec le stéréotype « interface » (cette forme est appelée la forme développée).

## Les relations

### L’association

L’association indique que deux éléments sont reliés entre eux.

Une association peut avoir un nom qui décrit la nature de la relation. Il est
possible de donner une direction au nom pour éviter toute ambiguïté.

Une association peut préciser combien d’objets peuvent être reliés de part et d’autre.
On parle de *multiplicité*. Une multiplicité peut être un nombre fixe (3), un
ensemble fini de valeurs (0..3) ou un ensemble infini de valeurs (1..n) ou (1..\*).

Une association peut limiter le sens de navigation. Pour cela, il suffit de représenter
l’association sous la forme d’une flèche indiquant le sens de navigation. Le sens de navigation
traduit qu’une relation n’a besoin pas d’être réciproque pour que le système fonctionne correctement.

Une association implique que les deux éléments reliés jouent un rôle particulier
l’un envers l’autre. Il est possible d’indiquer le rôle de chaque élément de part
et d’autre de l’association.

Une relation d’association peut déclarer une relation d’une classe vers elle-même.

### La dépendance

Une dépendance est une relation d’utilisation. Cela signifie que la modification d’un élément
peut entraîner une modification d’un autre élément. Les dépendances servent le plus souvent à montrer les classes
qui sont utilisées comme arguments dans la signature d’une opération.

Dans l’exemple ci-dessus, la classe *Gestionnaire* indique qu’elle est dépendante
de la classe  *ÉvénementIHM* probablement parce qu’elle utilise un objet de cette classe
comme paramètre d’une opération.

### L’agrégation

L’agrégation est une association qui représente la notion du « tout/partie ». Elle
permet d’indiquer qu’un élément *possède* un autre élément. Dans la représentation,
le losange est placé du côté de l’élément qui représente celui qui possède.

### La composition

La composition est une agrégation particulière qui traduit une relation forte
en terme de cycle de vie. L’élément qui représente la partie ne peut appartenir
qu’à un seul *tout* et sa durée de vie est conditionnée à la durée de vie de l’élément
qui le possède.

Dans l’exemple précédent, un objet de type *Adresse* ne peut être relié qu’à un seul
objet de type *Personne* et les deux objets ont la même durée de vie.

#### NOTE
Dans la programmation orientée objet, on ne distingue pas forcément les types
de relations de manière aussi avancée qu’avec UML. Très souvent, le terme
de *composition* est utilisé pour désigner tous les types d’association.

### La généralisation

La relation de généralisation définit une relation entre un élément général (appelé
souvent *super classe* ou *classe mère*) et un élément plus spécifique (appelé souvent
*sous-classe* ou *classe fille*). La généralisation définit une relation de type
« est un » ou « est une ».

#### NOTE
Dans les langages de programmation orientés objet, le mécanisme
permettant de réaliser ce type de relation est appelé *l’héritage*.

### La réalisation

La réalisation permet d’indiquer qu’un classe réalise le contrat décrit par l’interface.
La réalisation peut se noter dans une forme simple dans laquelle l’interface est représentée
par un cercle. La réalisation peut également se noter dans une forme développée (la relation
est alors représentée par un trait en pointillé terminé par un triangle creux).

#### NOTE
Dans les langages de programmation orientés objet, la réalisation est souvent appelée *implémentation*.

## Les diagrammes

### Le diagramme de classes

Le diagramme de classes représente un ensemble de classes et d’interfaces ainsi que leurs relations.
Il est le diagramme le plus couramment utilisé et il offre une représentation assez proche de
l’implémentation dans un langage de programmation. Le diagramme de classes fait partie des diagrammes
structurels d’UML car il ne permet pas de représenter la dynamique d’un système ni de comprendre
son évolution dans le temps.

Ci-dessous, deux exemples simples de diagrammes de classes :

### Le diagramme de séquence

Le diagramme de séquence permet de visualiser la succession des appels d’opérations entre les objets.
Il fait partie des diagrammes d’interaction d’UML car il permet d’illustrer la dynamique d’un
système.

Le diagramme de séquence représente la ligne de vie des objets sur une ligne verticale et les échanges
entre les objets (les appels à des opérations) par des flèches horizontales. Lorsqu’une opération
d’un objet est appelée, ce dernier devient actif (représenté sur sa ligne de vie par un rectangle).

Il est possible d’encadrer certaines parties d’un diagramme de séquence par des **fragments d’interaction**.
Un fragment d’interaction a un nom pour désigner sa fonction. Dans l’exemple ci-dessous, on utilise le fragment
**loop** pour signaler une répétition de la séquence pour un ensemble d’éléments d’une collection (ici les objets
de type *Personne*).
