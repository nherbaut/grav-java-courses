# Modules et contrôleurs

Nous avons vu au chapitre précédent que grâce aux directives nous pouvons associer
une vue à un modèle sans écrire réellement de code JavaScript. Pour des applications
complexes, il est cependant nécessaire d’avoir recours au JavaScript pour réaliser
le code applicatif.

[AngularJS](https://docs.angularjs.org/guide) repose sur le modèle de conception du [MVC](https://fr.wikipedia.org/wiki/Mod%C3%A8le-vue-contr%C3%B4leur). Les interactions entre
l’utilisateur et l’application se font au travers de contrôleurs qui vont pouvoir
mettre à jour le modèle. Il est très facile de créer ses propres contrôleurs
avec [AngularJS](https://docs.angularjs.org/guide). Pour cela il faut créer sa propre application sous la forme d’un module.

## Création d’un module

[AngularJS](https://docs.angularjs.org/guide) fournit un objet spécial : [angular](https://docs.angularjs.org/api/ng/function). Cet objet dispose de plusieurs méthodes
utilitaires dont la méthode [angular.module()](https://docs.angularjs.org/api/ng/function/angular.module). Cette méthode permet de créer
son propre module, c’est-à-dire son application :

```javascript
var app = angular.module("myapp", []);
```

#### NOTE
Le second paramètre de la méthode sert à lister les noms des modules dont
le module est dépendant. Il n’est pas nécessaire de lister les modules par
défaut fournis par [AngularJS](https://docs.angularjs.org/guide).

Attention, si vous omettez le second paramètre, la méthode [angular.module()](https://docs.angularjs.org/api/ng/function/angular.module)
ne crée pas le module mais se contente de le retourner s’il existe.

Pour utiliser ce module, il faut le nommer explicitement dans la directive
[ngApp](https://docs.angularjs.org/api/ng/directive/ngApp). Ainsi, [AngularJS](https://docs.angularjs.org/guide) créera une application basée sur ce module.

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">
</body>
</html>
```

## Création d’un contrôleur

Un contrôleur est un objet JavaScript. On utilise la méthode [angular.Module.controller()](https://docs.angularjs.org/api/ng/type/angular.Module#controller)
pour enregistrer un contrôleur dans un module en fournissant son nom ainsi
qu’une méthode de construction.

```javascript
var app = angular.module("myapp", []);

app.controller("helloController", function() {
    this.message = "";
    this.name = "the world";

    this.sayHello = function() {
        this.message = "Hello " + this.name;
    }
});
```

Il est ensuite possible de créer un contrôleur pour une portion de la vue grâce à la directive
[ngController](https://docs.angularjs.org/api/ng/directive/ngController).

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">
    <div ng-controller="helloController as ctrl">
        <div>
            <input ng-model="ctrl.name">
            <button ng-click="ctrl.sayHello()">Valider</button>
        </div>

        <div>{{ ctrl.message }}</div>
    </div>
</body>
</html>
```

À la ligne 9, on utilise la directive [ngController](https://docs.angularjs.org/api/ng/directive/ngController) pour créer un contrôleur
*helloController* que l’on nomme *ctrl*. On utilise ensuite les directives
[ngModel](https://docs.angularjs.org/api/ng/directive/ngModel) et [ngClick](https://docs.angularjs.org/api/ng/directive/ngClick) pour permettre de modifier un attribut du contrôleur
et lancer l’appel à la méthode *sayHello()*.

## La notion de scope

Un notion importante dans [AngularJS](https://docs.angularjs.org/guide) est celle de *scope*. Il n’existe pas à proprement
parlé un seul modèle dans notre application mais une hiérarchie de portée. À la
création de l’application, [AngularJS](https://docs.angularjs.org/guide) initialise la portée racine appelée **$rootScope**.
Puis, à chaque fois que nous appelons une directive, cette dernière est susceptible
de créer une nouvelle portée. Une portée hérite des attributs et des méthodes de la
portée précédente (il s’agit d’un
[héritage par prototype](https://developer.mozilla.org/fr/docs/Web/JavaScript/H%C3%A9ritage_et_cha%C3%AEne_de_prototypes)
tel que défini par JavaScript).

Ainsi la directive [ngController](https://docs.angularjs.org/api/ng/directive/ngController) crée une nouvelle portée dans laquelle sera placée
l’instance du contrôleur sous le nom *ctrl*. Cette porté n’existe
que pour l’élément qui contient la directive [ngController](https://docs.angularjs.org/api/ng/directive/ngController) et ses éléments fils
dans la page HTML.

La portée courante est appelée **$scope** et la portée parente **$parentScope**.

[AngularJS](https://docs.angularjs.org/guide) fournit une moteur d’injection qui permet de recevoir en paramètres les objets
nécessaires. Ainsi, un contrôleur peut, lors de sa construction, demander à recevoir
le paramètre **$scope** correspondant à sa portée. Nous pouvons revoir l’implémentation
précédente afin d’ajouter directement les attributs et les méthodes dans le modèle :

```javascript
var app = angular.module("myapp", []);

app.controller("helloController", function($scope) {
    $scope.message = "";
    $scope.name = "the world";

    $scope.sayHello = function() {
        $scope.message = "Hello " + $scope.name;
    }
});
```

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">

    <div ng-controller="helloController">
        <div>
            <input ng-model="name">
            <button ng-click="sayHello()">Valider</button>
        </div>

        <div>{{ message }}</div>
    </div>

</body>
</html>
```

Nous n’avons plus besoin de nommer explicitement le contrôleur dans la directive
[ngController](https://docs.angularjs.org/api/ng/directive/ngController) car nous accédons dans cet exemple aux attributs *name* et *message*
ainsi qu’à la méthode *sayHello()* directement depuis le modèle.

#### NOTE
Même si cette dernière syntaxe est plus concise dans l’implémentation HTML, il est
recommandé de placer les attributs et les méthodes dans le contrôleur (comme
dans le premier exemple) plutôt que directement dans le modèle.

<a id="injection-service"></a>

## Injection de services

[AngularJS](https://docs.angularjs.org/guide) fournit des composants particuliers appelés
[services](https://docs.angularjs.org/api/ng/service). Chaque service offre
des fonctionnalités particulières. Un contrôleur peut recevoir un service par
injection lors de sa construction. Pour cela, il suffit de spécifier un paramètre
portant le même nom que le service.

Par exemple, il existe un service appelé [$timeout](https://docs.angularjs.org/api/ng/service/$timeout). Ce service encapsule la fonction
`window.setTimeout` et permet de définir une fonction à appeler après une période
de temps en millisecondes.

```javascript
var app = angular.module("myapp", []);

app.controller("delaiController", function($scope, $timeout) {
    $scope.message= "";
    $scope.delai= 5000;

    function finDelai() {
        $scope.message = "C'est fini !";
    }

    $timeout(finDelai, $scope.delai);
});
```

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">
    <div ng-controller="delaiController">
        <div ng-hide="message">Un message s'affichera dans {{ delai }} ms.</div>
        <div>{{ message }}</div>
    </div>
</body>
</html>
```

#### NOTE
Par convention le nom des services fournis par [AngularJS](https://docs.angularjs.org/guide) commence par **$**.

#### NOTE
Nous verrons qu’il existe d’autres services fournis [AngularJS](https://docs.angularjs.org/guide) et surtout qu’il
est possible de créer ses propres services.

## Injection et minification

Le moteur d’injection de [AngularJS](https://docs.angularjs.org/guide) se base sur le nom des paramètres pour déduire
l’objet à injecter. Il existe cependant une pratique courante qui consiste
à *minifier* le code JavaScript, c’est-à-dire à utiliser un dispositif de réécriture
pour supprimer les caractères inutiles. Ce type d’outil remplace automatiquement
le nom des paramètres par un nom plus court. Par exemple, il pourrait renommer
le paramètre  *$scope* par *a*. Renommer un paramètre n’a pas d’incidence sur l’exécution
du code mais, par contre, cela empêche le moteur d’injection de [AngularJS](https://docs.angularjs.org/guide) de fonctionner
correctement. Pour pallier à ce problème, plutôt que de passer directement une méthode
de construction à la méthode *anguler.Module.controller()*, il est possible
de passer un tableau dont les premiers éléments contiennent le nom des paramètres
sous la forme de chaînes de caractères et le dernier élément est la méthode de construction.
Comme un outil de minification ne modifie pas le contenu des chaînes de caractères,
le moteur d’injection de [AngularJS](https://docs.angularjs.org/guide) peut utiliser cette information sans risque d’erreur.

Si nous reprenons l’exemple du contrôleur utilisant le service [$timeout](https://docs.angularjs.org/api/ng/service/$timeout), pour
éviter des problèmes liés à la minification, nous devrions implémenter le contrôleur
de la façon suivante :

```javascript
var app = angular.module("myapp", []);

app.controller("delaiController", ["$scope", "$timeout", function($scope, $timeout) {
    $scope.message= "";
    $scope.delai= 5000;

    function finDelai() {
        $scope.message = "C'est fini !";
    }

    $timeout(finDelai, $scope.delai);
}]);
```

#### WARNING
Il faut faire très attention à respecter le même ordre des paramètres dans la liste
de noms du tableau et dans la liste effective des paramètres.

## Déclaration de constantes

Pour des raisons de lisibilité, on peut souhaiter isoler certaines valeurs sous
la forme de constantes. Avec [AngularJS](https://docs.angularjs.org/guide), il est possible de traiter une constante comme
une valeur que l’on peut injecter (par exemple dans un contrôleur). Une constante
se déclare grâce à la méthode [angular.Module.constant()](https://docs.angularjs.org/api/ng/type/angular.Module#constant).

```javascript
var app = angular.module("myapp", []);

app.constant("MESSAGE", "Hello the world");

app.controller("messageController", ["$scope", "MESSAGE", function($scope, MESSAGE) {
    $scope.message = MESSAGE;
}]);
```
