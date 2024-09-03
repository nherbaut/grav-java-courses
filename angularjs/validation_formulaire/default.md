# Validation de formulaire

[AngularJS](https://docs.angularjs.org/guide) peut être utilisé pour valider côté client les données d’un formulaire.
Il se base sur la validation HTML 5 tout en ajoutant des fonctionnalités
supplémentaires.

## État d’un formulaire

Il faut se rappeler que dans une application [AngularJS](https://docs.angularjs.org/guide), certaines balises sont
traitées comme des directives et sont accessibles dans une expression par leur
nom (la valeur de l’attribut `name`). C’est le cas des balises `<form>`,
`<input>`, `<textarea>` et `<select>`.

[AngularJS](https://docs.angularjs.org/guide) définit un certain nombre d’état pour un formulaire et ses éléments. À travers
le modèle, on peut consulter les différents états sous la forme d’une valeur booléenne
pour savoir si le formulaire ou un de ses éléments est dans un état donné. De plus,
[AngularJS](https://docs.angularjs.org/guide) met à jour l’attribut `class` du formulaire et de chacun de ses éléments
pour refléter leur état.

Pour une balise `<form>` :

#### État d’un formulaire

| État dans le modèle   | Classe CSS    | Description                                                |
|-----------------------|---------------|------------------------------------------------------------|
| `$pristine`           | `ng-pristine` | Aucun champ du formulaire n’a pas été modifié              |
| `$dirty`              | `ng-dirty`    | Un ou plusieurs champs ont été modifiés                    |
| `$invalid`            | `ng-invalid`  | Le contenu de certains champs du formulaire sont invalides |
| `$valid`              | `ng-valid`    | Le contenu du formulaire est valide                        |
| `$submitted`          | N/A           | Le contenu du formulaire a été soumis                      |

Pour les éléments d’un formulaire :

#### État d’un élément du formulaire

| État dans le modèle   | Classe CSS     | Description                                      |
|-----------------------|----------------|--------------------------------------------------|
| `$untouched`          | `ng-untouched` | Le champ n’a pas été touché                      |
| `$touched`            | `ng-touched`   | Le champ a été touché                            |
| `$pristine`           | `ng-pristine`  | Le champ n’a pas été modifié                     |
| `$dirty`              | `ng-dirty`     | Le champ a été modifié                           |
| `$invalid`            | `ng-invalid`   | Le contenu du champ est invalide                 |
| `$valid`              | `ng-valid`     | Le contenu du champ est valide                   |
| `$pending`            | `ng-pending`   | Le contenu du champ est en attente de validation |

Les états sont accessibles depuis le modèle et peuvent servir à modifier
le rendu du formulaire.

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    <form name="f">
        <input name="nom" ng-model="nom">
        <span ng-if="f.nom.$touched">Touché !</span>
    </form>
    <div ng-if="f.$dirty">Vous modifiez quelque chose</div>
</body>
</html>
```

#### WARNING
Un élément d’un formulaire qui ne possède pas la directive `ng-model` est
ignoré par [AngularJS](https://docs.angularjs.org/guide). Donc sont état ne sera pas pris en compte.

## Les directives de validation

[AngularJS](https://docs.angularjs.org/guide) fournit des directives destinées spécifiquement à valider le contenu
d’un formulaire. Si ces directives ne sont pas respectées, alors l’élément
associé passe à l’état invalide.

Les directives disponibles sont :

[ngMaxLength](https://docs.angularjs.org/api/ng/directive/ngMaxlength)
: Donne la longueur maximale d’un champ.

[ngMinLength](https://docs.angularjs.org/api/ng/directive/ngMinlength)
: Donne la longueur minimale d’un champ.

[ngPattern](https://docs.angularjs.org/api/ng/directive/ngPattern)
: Définit une expression régulière (ou une chaîne de caractères contenant
  une expression régulière) à laquelle la valeur de l’élément doit se conformer.

required
: Indique que le champ est obligatoire. On peut également utiliser la directive
  [ngRequired](https://docs.angularjs.org/api/ng/directive/ngRequired) pour définir dynamiquement si le champ doit être obligatoire.

min
: La valeur minimale d’un nombre.

max
: La valeur maximale d’un nombre.

De plus, [AngularJS](https://docs.angularjs.org/guide) utilise le type d’un champ `<input>` (number, range…) pour ajouter
des contrôles supplémentaires.

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="utf-8">
    <title>title</title>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.9/angular.min.js"></script>
</head>
<body ng-app>
    <form name="f">
        <input name="code" ng-model="code" ng-pattern="/\d{2}.*/">
        <span ng-show="f.code.$invalid">Le code doit commencer par deux chiffres</span><br>
        <input name="nom" ng-model="nom" required>
        <span ng-show="f.nom.$touched && f.nom.$invalid">Le nom est obligatoire</span><br>
        <input name="note" type="number" ng-model="note" min="0" max="10">
        <span ng-show="f.note.$invalid">La note doit être comprise entre 0 et 10</span><br>
    </form>
</body>
</html>
```
