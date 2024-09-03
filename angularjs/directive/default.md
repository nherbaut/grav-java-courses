# Les directives

Les directives sont au cœur des applications [AngularJS](https://docs.angularjs.org/guide). S’il existe déjà une liste
importante de directive, il est tout à fait possible de créer ses propres directives.

#### NOTE
Pour une documentation complète sur la création de directives, reportez-vous
à la [documentation officelle](https://docs.angularjs.org/guide/directive).

## Une première directive

La création d’une directive peut s’avérer complexe car [AngularJS](https://docs.angularjs.org/guide) offre une grande
souplesse dans leur création.

Pour commencer, nous pouvons considérer qu’une directive est définie par les
propriétés suivantes :

restrict
: Cette propriété définit où il est possible de déclarer la directive dans
  le page HTML. Cette propriété est une chaîne de caractères pouvant contenir
  les lettres suivantes :
  <br/>
  E
  : Pour signaler que la directive est utilisable comme élément (balise) dans
    la page HTML.
  <br/>
  A
  : Pour signaler que la directive est utilisable comme attribut d’un élément
    dans la page HTML.
  <br/>
  C
  : Pour signaler que la directive est utilisable dans la définition de
    l’attribut `class` d’un élément dans la page HTML. Cette possibilité
    est très peu utilisée… sauf pour la directive [ngClass](https://docs.angularjs.org/api/ng/directive/ngClass).
  <br/>
  M
  : Pour signaler que la directive est utilisable dans un commentaire de
    la page HTML. Cette possibilité n’est pas réellement utilisée en pratique.
  <br/>
  Une même directive peut autoriser plusieurs options. Par exemple `"EA"`
  pour signaler que la directive peut apparaître comme élément ou comme attribut
  d’un élément dans la page HTML.

template
: Cette propriété donne un *template* HTML qui sera inséré par la directive. Ce
  *template* peut contenir des expressions ou des directives [AngularJS](https://docs.angularjs.org/guide).

templateUrl
: Cette propriété donne l’URL du *template* HTML qui sera chargé depuis le serveur.

replace
: Une valeur booléenne pour indiquer si le résultat de la directive remplace
  l’élément qui contient la directive.

Pour déclarer une directive, on utilise la méthode [angular.Module.directive()](https://docs.angularjs.org/api/ng/type/angular.Module#directive).

```javascript
var app = angular.module("myapp", []);

app.directive("hello", function() {
    return {
        restrict: "AE",
        template: "<div>Hello AngularJS</div>",
        replace: true
    }
});
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body data-ng-app="myapp">
    <!-- Utilisation de la directive comme élément -->
    <div><hello></div>

    <!-- Utilisation de la directive comme attribut -->
    <div hello></div>
</body>
</html>
```

## Directive et scope

Dans une application [AngularJS](https://docs.angularjs.org/guide), le modèle est représenté par un contexte, ou plus
exactement par une hiérarchie de contextes. Le contexte courante est nommé
`$scope`. Une directive peut soit utiliser le contexte courant soit utiliser
un nouveau contexte soit utiliser un contexte isolé. Lors de la création de la
directive, on préciser l’attribut `scope` qui peut avoir les valeurs suivantes :

false
: Signifie que la directive utilisera le contexte actuel.

true
: Signifie qu’un nouveau contexte sera créé pour cette directive. Ce contexte
  héritera du contexte parent.

Un objet JavaScript
: Signifie que la directive utilisera un contexte isolé. Ce contexte n’a
  aucune relation avec le contexte courant. Le contenu du contexte est défini
  par l’objet JavaScript passé comme valeur.

Pour un contexte isolé, les attributs du contexte peuvent avoir des valeurs
spéciales qui permettent d’activer un liaison (*binding*).

`@`
: Permet de lier l’attribut à un attribut DOM du même nom. La valeur de cet
  attribut sera interprété comme une chaîne de caractères (pouvant contenir
  des expressions d’interpolation {{ }}). Ce lien est unidirectionnel.

`=`
: Permet de lier l’attribut à un attribut DOM du même nom. La valeur de cet
  attribut sera interprété comme le nom d’un propriété. Ce lien est bidirectionnel.

`&`
: Permet de lier l’attribut à un attribut DOM du même nom. La valeur de cet
  attribut sera interprété comme une fonction à appeler dans la directive.

Ci-dessous un exemple de directive liant un attribut à une expression :

```javascript
var app = angular.module("myapp", []);

app.directive("hello", function() {
    return {
        restrict: "AE",
        scope: {name: '@'},
        template: "<div>Hello {{name}}</div>",
        replace: true
    }
});
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body data-ng-app="myapp">
    <input ng-model=username>
    <hello name="{{username | uppercase}}">
</body>
</html>
```

Ci-dessous un exemple de directive liant un attribut à un attribut du modèle :

```javascript
var app = angular.module("myapp", []);

app.directive("hello", function() {
    return {
        restrict: "AE",
        scope: {modelName: '='},
        template: "<div>Hello {{modelName}}</div>",
        replace: true
    }
});
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body data-ng-app="myapp">
    <input ng-model=username>
    <hello model-name="username">
</body>
</html>
```

## Directive et contenu

Une directive utilise parfois le contenu de l’élément qui la déclare. C’est le
cas notamment de la directive [ngRepeat](https://docs.angularjs.org/api/ng/directive/ngRepeat) qui utilise le contenu de l’élément comme
*template* à répéter pour chaque élément d’une collection.

Pour créer une directive capable de manipuler les éléments DOM fils de la directive,
il faut ajouter positionner l’attribut `transclude` à `true`. On peut ensuite
utilise la directive [ngTransclude](https://docs.angularjs.org/api/ng/directive/ngTransclude) dans le *template* de la directive pour indiquer
la position à laquelle les éléments fils doivent être placés.

```javascript
var app = angular.module("myapp", []);

app.directive("hello", function() {
    return {
        restrict: "AE",
        transclude: true,
        template: "<div>Bonjour <span ng-transclude></span></div>",
        replace: true
    }
});
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body data-ng-app="myapp">
    <input ng-model=username>
    <div hello>
        monsieur ou madame {{username}}
    </div>
</body>
</html>
```

## Compilation et liaison

[AngularJS](https://docs.angularjs.org/guide) gère le cycle de vie d’une directive en deux étapes : la *compilation*
et la *liaison* (*link*).

Pendant l’étape de compilation, [AngularJS](https://docs.angularjs.org/guide) recherche les directives présentes dans la
vue en parcourant l’arbre DOM à partir de sa racine. L’étape de compilation réalise
par exemple le téléchargement et l’ajout du *template* dans l’arborescence DOM.
Chaque directive fournit lors de sa compilation une fonction de liaison
(*link function*) qui est collecté par [AngularJS](https://docs.angularjs.org/guide) comme résultat de la compilation.

Pendant l’étape de liaison, [AngularJS](https://docs.angularjs.org/guide) invoque les méthodes de liaison dans
**l’ordre inverse** de leur collecte. L’étape de liaison sert à créer la portée
(si nécessaire) et à mettre en place le *binding*. Si la directive est associée
à un contrôleur, ce dernier est créé et passé en paramètre de la fonction de liaison.

Une directive peut fournir ses propres fonctions de compilation, de liaison
et un contrôleur associé. Pour cela, il suffit de rajouter dans la déclaration
de la directive les propriété suivantes :

compile
: fournit la fonction de compilation de la directive. Cette fonction à la signature
  suivante :
  <br/>
  ```javascript
  function compile(element, attrs) {
      // ...
  }
  ```
  <br/>
  Les arguments de cette fonction sont :
  <br/>
  element
  : un objet qui représente l’élément DOM de la directive. [AngularJS](https://docs.angularjs.org/guide)
    fournit sa propre API de manipulation d’élément qui est très proche
    de celle de [JQuery](https://jquery.com). Reportez-vous à la [documentation](https://docs.angularjs.org/api/ng/function/angular.element).
  <br/>
  attr
  : un objet représentant les attributs de l’élément DOM de la directive.
  <br/>
  La fonction de compilation doit retourner la fonction de liaison de la directive.
  Cette fonction sera appelée par [AngularJS](https://docs.angularjs.org/guide) lors de la phase de liaison (*link*).

link
: fournit la fonction de liaison de la directive. Cet attribut est utile lorsque
  l’on ne désire par fournir de fonction de compilation. Cette fonction à
  la signature suivante :
  <br/>
  ```javascript
  function compile(scope, element, attrs, controller) {
      // ...
  }
  ```
  <br/>
  Les arguments de cette fonction sont :
  <br/>
  scope
  : la portée de la directive.
  <br/>
  element
  : un objet qui représente l’élément DOM de la directive. [AngularJS](https://docs.angularjs.org/guide)
    fournit sa propre API de manipulation d’élément qui est très proche
    de celle de [JQuery](https://jquery.com). Reportez-vous à la [documentation](https://docs.angularjs.org/api/ng/function/angular.element).
  <br/>
  attr
  : un objet représentant les attributs de l’élément DOM de la directive.
  <br/>
  controller
  : le contrôleur de la directive (et/ou les contrôleurs requis)

controller
: fournit la fonction de construction d’un contrôleur. Comme pour la création
  d’un contrôleur dans un module, cette dernière peut utiliser l’injection
  de dépendance en spécifiant des paramètres.
