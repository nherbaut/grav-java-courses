# Le module ngRoute

[AngularJS](https://docs.angularjs.org/guide) s’accompagne de plusieurs modules d’extension. Parmi eux, on trouve
[ngRoute](https://docs.angularjs.org/api/ngRoute) qui permet des créer une application mono page ou *Single Page Application* (SPA).

## Ajout du module ngRoute

Pour utiliser le module [ngRoute](https://docs.angularjs.org/api/ngRoute), vous devez l’ajouter à votre projet. Si vous
utilisez [Bower](https://bower.io/) pour gérer les sources Web, vous pouvez exécuter la commande :

```shell
bower install angular-route --save
```

Ensuite, il faut penser à ajouter dans les sources HTML une balise `<script>`
pour charger le fichier source du module :

```html
<script src="bower_components/angular-route/angular-route.min.js"></script>
```

#### NOTE
Pour des raisons de portabilité, les exemples de ce chapitre référencent
le module [ngRoute](https://docs.angularjs.org/api/ngRoute) à partir d’une URI CDN (Content Delivery Network).

Notre module d’application doit ensuite être créé en précisant qu’il dépend
du module [ngRoute](https://docs.angularjs.org/api/ngRoute). Pour cela on l’ajoute dans le tableau passé en
second paramètre à la méthode [angular.module()](https://docs.angularjs.org/api/ng/function/angular.module) :

```javascript
var app = angular.module("myapp", ["ngRoute"]);
```

## Objectif du module ngRoute

Le module [ngRoute](https://docs.angularjs.org/api/ngRoute) permet de définir des routes, c’est-à-dire des liens permettant
de modifier une portion de la page (appelée la vue). La modification peut se faire
en interrogeant le serveur de manière à récupérer la nouvelle portion de page. Ainsi
il est possible de créer une navigation tout en restant, pour l’utilisateur, dans la même page.

## Un exemple d’utilisation de ngRoute

L’exemple ci-dessous permet d’illustrer simplement le fonctionnement du module [ngRoute](https://docs.angularjs.org/api/ngRoute).
L’application propose trois liens à l’utilisateur. Lorsque ce dernier clique sur un lien,
il active une route qui réalise la mise à jour de la vue à partir d’un *template*.

```javascript
var app = angular.module("myapp", ["ngRoute"]);

app.config(["$routeProvider", function($routeProvider){
    $routeProvider
    .when("/", {
        template: "Bienvenue"
    })
    .when("/bonjour", {
        template: "Bonjour à tous"
    })
    .when("/bonsoir", {
        template: "Bonsoir à tous"
    })
}]);
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular-route.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">
    <div>
        <a href="#!/">Accueil</a>
        <a href="#!/bonjour">Dire bonjour</a>
        <a href="#!/bonsoir">Dire bonsoir</a>
    </div>
    <div ng-view></div>
</body>
</html>
```

Le module [ngRoute](https://docs.angularjs.org/api/ngRoute) fournit le *provider* [$routeProvider](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider) qui permet de déclarer
les routes. C’est ce qui est fait dans le fichier JavaScript grâce à la méthode
[when](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider#when) qui prend en premier paramètre la route et en second paramètre un objet
qui décrit le comportement attendu lorsque cette route est activée. Dans cet
exemple, on spécifie directement le *template* de contenu HTML.

Dans le fichier HTML de l’application, on définit aux lignes 11, 12 et 13 des balises
`<a>` pointant sur les routes. Une route est en fait une ancre dont le nom commence
par **!** (point d’exclamation). Une route n’est donc pas un chemin de ressource sur le serveur
mais plutôt une portion de la page. À la ligne 15, la directive [ngView](https://docs.angularjs.org/api/ngRoute/directive/ngView) indique
la position de la vue dans la page et donc la portion qui doit être mise à jour
lorsque l’utilisateur clique sur un lien.

## HTML 5 et URI

Depuis HTML 5, le module [ngRoute](https://docs.angularjs.org/api/ngRoute) peut utiliser des URI quelconques (inutile
de passer par des ancres au format `#!/...`. Cependant, pour que cela
fonctionne, il faut activer dans [AngularJS](https://docs.angularjs.org/guide) le mode HTML 5 grâce à la méthode
[$locationProvider.html5Mode()](https://docs.angularjs.org/api/ng/provider/$locationProvider#html5Mode). Pour défaut, la page HTML doit contenir
une balise `<base>` pour permettre au module [$location](https://docs.angularjs.org/api/ng/service/$location) de construire correctement
les URI.

```javascript
var app = angular.module("myapp", ["ngRoute"]);

app.config(["$locationProvider", "$routeProvider", function($locationProvider, $routeProvider){
    $routeProvider
    .when("/index.html", {
        template: "Bienvenue"
    })
    .when("/bonjour", {
        template: "Bonjour à tous"
    })
    .when("/bonsoir", {
        template: "Bonsoir à tous"
    })

    $locationProvider.html5Mode(true);
}]);
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <base href="/index.html">
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular-route.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">
    <div>
        <a href="/">Accueil</a>
        <a href="/bonjour">Dire bonjour</a>
        <a href="/bonsoir">Dire bonsoir</a>
    </div>
    <div ng-view></div>
</body>
</html>
```

#### NOTE
Pour fonctionner correctement, le mode HTML 5 implique également une configuration
adéquate du serveur. En effet, toutes les URI des routes doivent être connues
du serveur qui doit charger à chaque fois la même page d’application. [AngularJS](https://docs.angularjs.org/guide) se chargera
de déterminer la bonne route à activer pour l’affichage de l’application.

Même si la mise en place de ce type de solution est plus exigeante, cela
permet de concevoir une application SPA (*Single Page Application*) pour laquelle
la barre d’adresse est mise à jour selon les différentes vues. Un utilisateur
peut ainsi sauvegarder un marque page pour une route particulière.

## Contrôleur de route

Lorsque l’on configure les routes, il est possible de désigner le *template* par
une URI de façon à récupérer ce *template* sur le serveur. On peut également forcer
l’activation d’un contrôleur lors de l’activation de la route. De cette façon, il
est possible de concevoir des applications complexes :

```javascript
app.config(["$routeProvider", function($routeProvider){
    $routeProvider
    .when("/index.html", {
        templateUrl: "templates/accueil.html.tpl"
    })
    .when("/users", {
        templateUrl: "templates/users.html.tpl",
        controller: "userController"
    })
    .when("/bonsoir", {
        templateUrl: "templates/admin.html.tpl",
        controller: "adminController"
    })
}]);
```

#### NOTE
l’attribut `controller` est soit le nom d’un contrôleur déclaré par ailleurs
soit la méthode de construction du contrôleur (dans ce cas, le contrôleur
est anonyme).

## Paramètres de route

Le chemin d’une route peut contenir des paramètres signalées par `:`. La valeur
des ces paramètres est accessible grâce au service [$routeParams](https://docs.angularjs.org/api/ngRoute/service/$routeParams), par exemple
en injectant ce service dans un contrôleur associé. Le service [$routeParams](https://docs.angularjs.org/api/ngRoute/service/$routeParams)
permet également de récupérer les paramètres d’une route passés en paramètres d’URI :

```javascript
var app = angular.module("myapp", ["ngRoute"]);

app.controller("routeController", ["$routeParams", function($routeParams) {
    this.name = $routeParams.name;
}]);

app.config(["$locationProvider", "$routeProvider", function($locationProvider, $routeProvider){
    $routeProvider
    .when("/index.html", {
        template: "Bienvenue"
    })
    .when("/bonjour/:name", {
        template: "<div>Bonjour {{ctrl.name}}</div>",
        controller: "routeController",
        controllerAs: "ctrl"
    })
    .when("/bonsoir", {
        template: "<div>Bonsoir {{ctrl.name}}</div>",
        controller: "routeController",
        controllerAs: "ctrl"
    })

    $locationProvider.html5Mode(true);
}]);
```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <base href="/index.html">
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular-route.js"></script>
    <script src="app/app.js"></script>
</head>
<body ng-app="myapp">
    <div>
        <a href="/index.html">Accueil</a>
        <a href="/bonjour/AngularJS">Dire bonjour</a>
        <a href="/bonsoir?name=AngularJS">Dire bonsoir</a>
    </div>
    <div ng-view></div>
</body>
</html>
```

Dans l’exemple ci-dessus, une route est également définie avec l’attribut
`controllerAs` qui permet de donner le nom de la variable du contrôleur créé.
Il est donc possible d’accéder à ce contrôleur dans le *template* de la route.
