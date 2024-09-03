# Introduction à AngularJS

[AngularJS](https://docs.angularjs.org/guide) est un framework JavaScript open source développé par Google. Il permet
de concevoir des interactions riches dans une page Web et plus spécifiquement
des SPA (*Single Page Application*).

Contrairement à des bibliothèques comme [JQuery](https://jquery.com), l’objectif de [AngularJS](https://docs.angularjs.org/guide) n’est
pas de fournir des fonctionnalités avancées pour manipuler le [DOM](https://www.w3schools.com/js/js_htmldom.asp). [AngularJS](https://docs.angularjs.org/guide) est
un framework [MVC](https://fr.wikipedia.org/wiki/Mod%C3%A8le-vue-contr%C3%B4leur) grâce auquel le développeur conçoit un modèle, des contrôleurs
et des services. À partir de là, il est possible de développer des vues (des pages
HTML) qui vont afficher les données du modèle. Une interaction utilisateur
(clique, survole du pointeurs de souris, entrée clavier…) peut solliciter un
contrôleur qui va mettre à jour le modèle. En retour, un mécanisme de *bind* va
déclencher une mise à jour de la vue en fonction des données modifiées dans le modèle.

## Installation de AngularJS

[AngularJS](https://docs.angularjs.org/guide) s’installe comme un fichier JavaScript dans les pages HTML qui le nécessitent.
Vous pouvez [télécharger le fichier](https://angularjs.org/) depuis le site ou
bien vous pouvez le déclarer comme dépendance grâce à [Bower](https://bower.io/) :

```shell
bower install angularjs --save
```

#### NOTE
[Bower](https://bower.io/) est un gestionnaire de dépendances et de packages pour les ressources
Web. [Bower](https://bower.io/) est accessible depuis [NPM](https://www.npmjs.com/) (le gestionnaire de packages de
Node.js).

```shell
npm install -g bower
```

Puis vous pouvez initialiser un projet géré par [Bower](https://bower.io/) en tapant la commande
suivante dans le répertoire du projet Web :

```shell
bower init
```

[Bower](https://bower.io/) installe les fichiers téléchargés dans les répertoire `bower_components`.
Pour [AngularJS](https://docs.angularjs.org/guide), il faut donc ajouter dans les fichiers HTML la balise `<script>`
suivante :

```html
<script src="bower_components/angular/angular.js"></script>
```

#### NOTE
Pour des raisons de portabilité, les exemples de ce chapitre référencent [AngularJS](https://docs.angularjs.org/guide) à
partir d’une URI CDN (*Content Delivery Network*).

## Premier exemple sans contrôleur

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    <div>
        <label>Nom :</label>
        <input type="text" ng-model="nom">
    </div>
    <div>Bonjour <span ng-bind="nom"> !</div>
</body>
</html>
```

[AngularJS](https://docs.angularjs.org/guide) permet d’ajouter des directives dans la page HTML (la vue). Ces directives
permettent de réaliser des mises à jour automatiques de la page. Certaines directives
sont facilement identifiables car elles sont préfixées par **ng-**. Cependant,
certaines balises HTML sont également considérées comme des directives par
[AngularJS](https://docs.angularjs.org/guide). C’est notamment le cas de la balise `<input>` pour notre exemple précédent.
La liste complète des directives supportées est disponible dans la
[documentation](https://docs.angularjs.org/api/ng/directive). Nous verrons
dans un chapitre avancé qu’il est possible de définir ses propres directives.

Pour l’exemple précédent, les directives sont les suivantes :

ng-app
: À la ligne 7, la balise `<body>` contient l’attribut `ng-app` qui est
  une directive permettant de signaler une portion de page gérée par [AngularJS](https://docs.angularjs.org/guide).
  Après le chargement de la page, [AngularJS](https://docs.angularjs.org/guide) recherche la directive `ng-app`
  qui permet de délimiter une application [AngularJS](https://docs.angularjs.org/guide).

input
: À la ligne 10, la balise `<input>` est bien une directive car elle est
  utilisée dans le corps d’une application [AngularJS](https://docs.angularjs.org/guide). Cela va permettre d’associer
  la valeur de l’input à une donnée du modèle.

ng-model
: À la ligne 10 toujours, la directive `ng-model` permet de lier la valeur
  de la balise `<input>` avec un attribut du modèle appelé `nom`. Nous
  n’avons pas besoin de créer le modèle, [AngularJS](https://docs.angularjs.org/guide) le fait implicitement pour nous :
  c’est ce qu’on appelle le `scope`. Le lien (*binding*) est bidirectionnel :
  toute modification de la valeur dans la balise `<input>` met à jour l’attribut
  du modèle et toute modification de l’attribut du modèle met à jour
  la valeur de la balise `<input>`

ng-bind
: À la ligne 12, la directive `ng-bind` permet de lier le contenu de la
  balise `<span>` avec  la valeur de l’attribut `nom` du modèle. Tout
  changement de la valeur de cet attribut entraîne une mise à jour du contenu
  de la balise.

Pour comprendre la notion de lien bidirectionnel (*binding*), vous pouvez essayer
l’exemple ci-dessous :

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    <div>
        <label>Nom :</label>
        <input type="text" ng-model="nom">
    </div>
    <div>
        <label>Nom :</label>
        <input type="text" ng-model="nom">
    </div>
</body>
</html>
```

Dans cet exemple, les deux directives [input](https://docs.angularjs.org/api/ng/directive/input) sont liées au même attribut de modèle.
Le changement de valeur d’un [input](https://docs.angularjs.org/api/ng/directive/input) entraîne le changement de valeur de l’attribut
du modèle qui entraîne la mise à jour de la valeur de l’autre [input](https://docs.angularjs.org/api/ng/directive/input).

## Normalisation des noms

[AngularJS](https://docs.angularjs.org/guide) utilise un mécanisme dit de *normalisation des noms*.
Les conventions de nommage ne sont pas nécessairement les mêmes en HTML et en JavaScript.

Par convention, lorsqu’une directive apparaît dans le code HTML, si son nom est
composé de plusieurs mots, alors ils sont séparés par un tiret (par exemple `ng-app`).

Par contre, dans le code JavaScript, le nom des directives se note en [écriture
dromadaire](https://fr.wikipedia.org/wiki/Camel_case) (par exemple [ngApp](https://docs.angularjs.org/api/ng/directive/ngApp)).

De plus, afin de permettre d’écrire avec [AngularJS](https://docs.angularjs.org/guide) des pages HTML valides, il est
possible de préfixer les directives par **data-** afin de respecter le mécanisme
HTML 5 d’ajout [d’attributs non standards](https://www.w3schools.com/tags/att_global_data.asp).

```html
<body data-ng-app>
...
</body>
```

## Expression et interpolation

[AngularJS](https://docs.angularjs.org/guide) fournit son propre [langage d’expression](https://docs.angularjs.org/guide/expression)
qui permet de définir une expression liée au modèle ou un appel de méthode
pour la gestion des événements.

```javascript
2 + 2
var1 + var2
maValeur > 0 ? "ok" : "ko"
items[index]
```

De plus, [AngularJS](https://docs.angularjs.org/guide) fournit un mécanisme d’interpolation qui permet d’écrire des expressions
n’importe où dans la section d’une page HTML gérée par [ngApp](https://docs.angularjs.org/api/ng/directive/ngApp). Pour cela, il
faut entourer l’expression avec **{{}}**.

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    2 + 3 = {{2 + 3}}
</body>
</html>
```

L’interpolation joue le même rôle que la directive [ngBind](https://docs.angularjs.org/api/ng/directive/ngBind) puisqu’elle permet
de créer une liaison entre une portion du texte HTML et le résultat de l’évaluation
de l’expression. Nous pouvons donc réécrire notre premier exemple en utilisant
une interpolation :

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    <div>
        <label>Nom :</label>
        <input type="text" ng-model="nom">
    </div>
    <div>Bonjour {{nom}} !</div>
</body>
</html>
```

À la ligne 12, nous avons remplacé la directive [ngBind](https://docs.angularjs.org/api/ng/directive/ngBind) par une interpolation
de l’expression `nom` pour lier le texte HTML à la valeur de l’attribut `nom`
dans le modèle.

## Quelques directives

Cette section présente quelques directives fournies par [AngularJS](https://docs.angularjs.org/guide).
Une directive peut apparaître dans une page HTML comme une balise ou un attribut
(et plus rarement comme une classe dans l’attribut `class`).
Pour une liste complète des directives disponibles, reportez-vous à la
[documentation officielle](https://docs.angularjs.org/api/ng/directive/).

Tout d’abord, [AngularJS](https://docs.angularjs.org/guide) fournit des directives qui portent le même nom que des
balises HTML. Il s’agit de : [a](https://docs.angularjs.org/api/ng/directive/a), [form](https://docs.angularjs.org/api/ng/directive/form), [textarea](https://docs.angularjs.org/api/ng/directive/textarea), [input](https://docs.angularjs.org/api/ng/directive/input), [script](https://docs.angularjs.org/api/ng/directive/script) et [select](https://docs.angularjs.org/api/ng/directive/select).
Cela signifie que ces balises sont comprises par [AngularJS](https://docs.angularjs.org/guide) qui en étend le comportement.
Hormis pour la balise [script](https://docs.angularjs.org/api/ng/directive/script), ces directives existent pour permettre une intégration
plus simple pour le développeur. Par exemple la directive [form](https://docs.angularjs.org/api/ng/directive/form) permet d’ajouter
des fonctionnalités avancées pour la validation de formulaire.

Ensuite, [AngularJS](https://docs.angularjs.org/guide) fournit une directive pour chaque événement HTML.
Ainsi on trouve les directives [ngBlur](https://docs.angularjs.org/api/ng/directive/ngBlur), [ngChange](https://docs.angularjs.org/api/ng/directive/ngChange), [ngClick](https://docs.angularjs.org/api/ng/directive/ngClick), [ngCopy](https://docs.angularjs.org/api/ng/directive/ngCopy), [ngCut](https://docs.angularjs.org/api/ng/directive/ngCut), [ngDblclick](https://docs.angularjs.org/api/ng/directive/ngDblclick),
[ngFocus](https://docs.angularjs.org/api/ng/directive/ngFocus), [ngKeydown](https://docs.angularjs.org/api/ng/directive/ngKeydown), [ngKeyup](https://docs.angularjs.org/api/ng/directive/ngKeyUp), [ngKeypress](https://docs.angularjs.org/api/ng/directive/ngKeypress), [ngMousedown](https://docs.angularjs.org/api/ng/directive/ngMousedown), [ngMouseup](https://docs.angularjs.org/api/ng/directive/ngMouseup), [ngMouseleave](https://docs.angularjs.org/api/ng/directive/ngMouseleave),
[ngMouseenter](https://docs.angularjs.org/api/ng/directive/ngMouseenter), [ngMousemove](https://docs.angularjs.org/api/ng/directive/ngMousemove), [ngMouseover](https://docs.angularjs.org/api/ng/directive/ngMouseover), [ngPaste](https://docs.angularjs.org/api/ng/directive/ngPaste), [ngSubmit](https://docs.angularjs.org/api/ng/directive/ngSubmit). Ces directives
permettent de spécifier des expressions [AngularJS](https://docs.angularjs.org/guide) et donc de lier un
événement à un appel d’une méthode de contrôleur. Nous verrons au chapitre suivant comment
déclarer et utiliser des contrôleurs dans notre application.

[AngularJS](https://docs.angularjs.org/guide) fournit également des directive pour certains attributs HTML qu’il peut être
utile de lier à des attributs du modèle pour gérer dynamiquement le style ou les liens.
On trouve ainsi la directive [ngSrc](https://docs.angularjs.org/api/ng/directive/ngSrc) pour les images, la directive [ngHref](https://docs.angularjs.org/api/ng/directive/ngHref) pour les liens
et plus généralement les directives [ngClass](https://docs.angularjs.org/api/ng/directive/ngClass) et [ngStyle](https://docs.angularjs.org/api/ng/directive/ngStyle) pour l’ensemble des éléments
HTML.

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <style>
        .theme-soft {
            background-color: white;
            color: black;
        }

        .theme-dark {
            background-color: black;
            color: white;
        }
    </style>
</head>
<body ng-app class="ng-class: classeFond">
    <div>
        <label>Thème :</label>
        <select ng-model="classeFond">
            <option value="">--</option>
            <option value="theme-soft">Thème clair</option>
            <option value="theme-dark">Thème sombre</option>
        </select>
    </div>

    <div>
        <label>Couleur :</label>
        <select ng-model="couleur">
            <option value="">--</option>
            <option value="red">rouge</option>
            <option value="yellow">jaune</option>
            <option value="green">vert</option>
            <option value="blue">bleu</option>
        </select>
    </div>
    <div ng-style="{'color': couleur}">Bonjour Angular !</div>

    <div>
        <label>Image :</label>
        <select ng-model="photo">
            <option value="">--</option>
            <option value="https://cdn.pixabay.com/photo/2018/02/15/21/55/sunset-3156440_960_720.jpg">Soleil</option>
            <option value="https://cdn.pixabay.com/photo/2017/12/18/01/41/the-sea-3025268_960_720.jpg">Océan</option>
            <option value="https://cdn.pixabay.com/photo/2017/01/18/21/34/cyprus-1990939_960_720.jpg">Arbres</option>
        </select>
    </div>
    <div><img ng-src="{{photo}}"></div>
</body>
</html>
```

Enfin, [AngularJS](https://docs.angularjs.org/guide) offre des directives particulières pour dynamiser le rendu de la page.

[ngApp](https://docs.angularjs.org/api/ng/directive/ngApp)
: Cette directive permet d’indiquer la portion de la page HTML qui est gérée
  par [AngularJS](https://docs.angularjs.org/guide) (il est possible de placer cette directive directement sur la
  balise `html` de la page). Elle permet d’initialiser [AngularJS](https://docs.angularjs.org/guide) sans avoir
  besoin d’écrire de code JavaScript. La directive [ngApp](https://docs.angularjs.org/api/ng/directive/ngApp) accepte comme
  valeur un nom d’application. Nous verrons au chapitre suivant que cela
  permet d’étendre le framework avec notre propre code. Notamment pour créer
  des contrôleurs.

[ngBind](https://docs.angularjs.org/api/ng/directive/ngBind)
: Cette directive permet de remplacer le contenu HTML d’un élément par le résultat
  de l’expression passée à [ngBind](https://docs.angularjs.org/api/ng/directive/ngBind). De plus cette directive crée une liaison (*binding*)
  qui assure que le contenu HTML de l’élément sera mis à jour lorsque l’expression
  sera réévaluée.

[ngDisabled](https://docs.angularjs.org/api/ng/directive/ngDisabled)
: Cette directive permet de gérer dynamiquement la valeur de l’attribut `disabled`
  pour les éléments HTML qui le supporte (`<input>`, `<button>`, `<select>`…).

[ngHide](https://docs.angularjs.org/api/ng/directive/ngHide) / [ngShow](https://docs.angularjs.org/api/ng/directive/ngShow)
: Ces directives permettent de cacher (respectivement afficher) un élément HTML
  si son expression est évaluée `true`.

[ngIf](https://docs.angularjs.org/api/ng/directive/ngIf)
: Cette directive crée l’arborescence HTML si son expression est évaluée à `true`.

[ngInclude](https://docs.angularjs.org/api/ng/directive/ngInclude)
: Cette directive insère un document à partir de l’URI donnée.

[ngRepeat](https://docs.angularjs.org/api/ng/directive/ngRepeat)
: Cette directive réalise une itération sur une liste et produit une arborescence
  [DOM](https://www.w3schools.com/js/js_htmldom.asp) pour chacun des éléments de la liste :
  <br/>
  ```html
  <!doctype html>
  <html>
  <head>
      <meta charset="utf-8">
      <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
  </head>
  <body ng-app>
      <ul>
          <li ng-repeat="item in ['Premier', 'Deuxième', 'Troisième']">{{item}}</li>
      </ul>
  </body>
  </html>
  ```

[ngModel](https://docs.angularjs.org/api/ng/directive/ngModel)
: Cette directive associe un élément `<input>`, `<select>` ou
  `<textarea>` avec un attribut du modèle. Elle crée un lien bidirectionnel
  (*binding*) entre l’élément et le modèle.

[ngModelOptions](https://docs.angularjs.org/api/ng/directive/ngModelOptions)
: Cette directive permet de modifier la manière dont la liaison doit se faire
  entre la directive [ngModel](https://docs.angularjs.org/api/ng/directive/ngModel) et le modèle. [ngModelOptions](https://docs.angularjs.org/api/ng/directive/ngModelOptions) accepte un objet
  JavaScript avec les options `updateOn` et `debounce`.
  <br/>
  updateOn
  : donne le nom de l’événement HTML qui déclenche la mise à jour du modèle.
    Par exemple, il est possible de spécifier la valeur `"blur"` pour
    réaliser la mise à jour lorsque l’élément perd le focus.
  <br/>
  debounce
  : donne en millisecondes un délai entre l’événement de mise à jour et la
    mise à jour du modèle. Cela permet un décalage entre la saisie et la
    mise à jour de la vue.
  <br/>
  #### NOTE
  [ngModelOptions](https://docs.angularjs.org/api/ng/directive/ngModelOptions) accepte aussi un attribut `timezone` si l’élément
  associé est un `<input>` représentant une date afin d’indiquer le fuseau
  horaire qui doit être utilisé pour afficher la date.

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    <input type="checkbox" ng-model="selectionne">Cliquez pour activer/désactiver<br>
    <label>Expéditeur</label>
    <input type="text" ng-model="expediteur" ng-model-options="{debounce: 500}"
                       ng-disabled="! selectionne">
    <div ng-show="selectionne">Ceci est un message de {{expediteur}}</div>
</body>
</html>
```
