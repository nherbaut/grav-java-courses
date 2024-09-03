---
published: false
sitemap:
    lastmod: '21-08-2024 23:15'
media:
    featured_image:
        toggle: true
        file: {  }
partials:
    header_subtitle:
        toggle: true
    metadata:
        where: header
    breadcrumbs:
        toggle: false
---

# HTTP : la négociation de contenu

La forme des messages échangés entre un client et un serveur n’est
absolument pas prescrite par HTTP. Par forme, il faut comprendre aussi
bien le format, le jeu de caractères (charset) mais aussi la langue
utilisée (s’il s’agit d’une forme textuelle). On dit que le client et le
serveur n’échangent que des **représentations**. Différents clients et
serveurs peuvent avoir des capacités ou des préférences différentes
concernant la forme d’une représentation. HTTP définit ainsi la
possibilité de décrire le contenu d’un message grâce aux en-têtes
[Content-type](https://tools.ietf.org/html/rfc7231#section-3.1.1.5)
et
[Content-Language](https://tools.ietf.org/html/rfc7231#section-3.1.3.2).
Mais HTTP permet aussi à un client et à un serveur de se mettre d’accord
sur la forme de représentation souhaitée. Ce mécanisme s’appelle la
**négociation de contenu**. On distingue deux méthodes de négociation de
contenu :

La négociation proactive
: Le serveur sélectionne la représentation la plus appropriée en
  fonction des préférences du client que ce dernier transmet dans la
  requête.

La négociation réactive
: Le serveur répond à un client non pas une représentation, mais une
  liste de liens vers des représentations en précisant la forme de
  chacune. Le client peut alors choisir parmi cette liste la
  représentation qui lui convient le mieux.

Dans la suite de ce chapitre, nous nous bornerons à détailler la
négociation de contenu proactive.

## La négociation de type de contenu

Parfois, un serveur peut disposer de plusieurs représentations pour une
même ressource. Par exemple, il peut présenter un série de données dans
une page HTML et sous un format CSV. Le client peut alors suggérer au
serveur une liste de formats MIME correspondant à ses préférences.
L’en-tête
[Accept](https://tools.ietf.org/html/rfc7231#section-5.3.2) permet
au client de fournir une liste de types MIME séparés par une virgule.
Bien évidemment, l’en-tête `Accept` n’a de sens que si le client
s’attend à recevoir une représentation de la part du serveur (ou s’il
utilise la méthode `HEAD` pour tester l’existence de ce format).

Négociation de contenu sur le type de représentation

```java
GET /individu/00001 HTTP/1.1
Host: www.monserveur.fr
Accept: text/html,text/plain,application/pdf
```

Dans l’exemple précédent, l’ordre dans la liste n’a pas d’importance, le
client annonce au serveur qu’il peut indifféremment fournir une
représentation au format HTML, texte brut ou un document PDF. Si le
serveur ne peut fournir aucune de ces représentations, il doit
considérer qu’il ne peut pas fournir de réponse acceptable pour ce
client. Dans ce cas, le serveur peut :

- Répondre par un code statut **406** (Not Acceptable)
- Ignorer l’en-tête et retourner une représentation de son choix. En
  effet, on considère que la décision de traiter ou non la réponse
  revient en dernier lieu au client.

Le client peut affiner sa négociation en annonçant que si plusieurs
représentations sont disponibles, certaines seront préférées à d’autres.
Cela se fait à l’aide du paramètre **q** (quality value) qui donne une
pondération entre 0 et 1. Lorsque le paramètre `q` n’est pas précisé,
sa valeur implicite est 1 (la plus forte préférence). Une valeur de 0
signifie que la représentation associée n’est pas acceptable pour ce
client.

Négociation de contenu avec pondération des formats

```java
GET /individu/00001 HTTP/1.1
Host: www.monserveur.fr
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

Dans l’exemple précédent, le client annonce au serveur qu’il préfére les
formats HTML et XHTML (q=1 est ici implicite) au format XML simple
(q=0.9). Sinon il accepte de recevoir n’importe quel format
(\*/\*;q=0.8). Ainsi, si un serveur dispose d’une représentation HTML,
XML et PDF pour une ressource, il doit retourner à ce client le format
HTML. Notez que cette valeur de l’en-tête `Accept` est celle par
défaut du navigateur Firefox.

## La négociation de jeu de caractères

Le client peut négocier avec le serveur le jeu de caractères (charset)
utilisé dans la réponse grâce à l’en-tête
[Accept-Charset](https://tools.ietf.org/html/rfc7231#section-5.3.3) :

Négociation de contenu sur l’encodage

```java
GET /individu/00001 HTTP/1.1
Host: www.monserveur.fr
Accept-Charset: iso-8859-1, utf-8;q=0.8, *;q=0.3
```

Si le serveur ne peut pas fournir de représentation utilisant un des
jeux caractères spécifiés par l’en-tête
[Accept-Charset](https://tools.ietf.org/html/rfc7231#section-5.3.3),
il doit considérer que sa représentation n’est pas acceptable par le
client. Dans ce cas, le serveur peut :

- Répondre par un code statut **406** (Not Acceptable)
- Ignorer l’en-tête et retourner une représentation utilisant le code
  caractères de son choix. En effet, on considère que la décision de
  traiter ou non la réponse revient en dernier lieu au client.

## La négociation de langue

Si la réponse contient des données textuelles destinées à être lues par
des individus, le client peut négocier la langue qui devrait être
utilisée grâce à l’en-tête
[Accept-Language](https://tools.ietf.org/html/rfc7231#section-5.3.5).
Si le serveur ne peut pas satisfaire les exigences du client, il est
tout de même sensé retourner ce qu’il juge la meilleure réponse afin de
ne pas empêcher l’utilisateur d’accéder à l’information.

Négociation de contenu sur la langue du document

```java
GET /individu/00001 HTTP/1.1
Host: www.monserveur.fr
Accept-Language: fr,fr-fr;q=0.8,en-us;q=0.5,en;q=0.3
```

La valeur de l’en-tête `Accept-Language` de l’exemple ci-dessus est
celle du navigateur Firefox pour un utilisateur français. On peut le
traduire de la façon suivante : le client préfère les documents écrits
en langue française (fr). Sinon, le client préfère les documents
français (fr-fr;q=0.8). Sinon, le client préfère les documents écrits en
américain (en-us;q=0.5). Sinon le client préfère les document écrits en
langue anglaise (en;q=0.3).

La négociation de contenu proactive pour la langue est une technique
utilisée pour internationaliser un site Web.

## L’en-tête Vary

L’en-tête
[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) est très
utile pour donner au client un indice sur le type de négociation de
contenu proactive qu’il peut réaliser. En effet, lorsqu’un serveur
dispose de plus d’une représentation et qu’il répond à un client, il
peut ajouter l’en-tête
[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) qui
indique la liste des en-têtes pouvant l’influencer dans son processus de
sélection et de représentation de la réponse.

Exemple de découverte d’une négociation de contenu proactive

```java
HEAD /individu/00001 HTTP/1.1
Host: www.monserveur.fr
```

```java
HTTP/1.1 200 OK
Host: www.monserveur.fr
Vary: Accept, Accept-Language
Content-type: text/plain;charset=utf-8
Content-Language: en-us
Content-length: 532
```

Dans l’exemple ci-dessus, le client peut savoir grâce à une requête
`HEAD` que le serveur retourne par défaut une représentation texte
brut en UTF-8 rédigée en américain. De plus, l’en-tête
[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) indique
que le serveur accepte la négociation de contenu proactive sur le type
de la représentation (`Accept`) et sur la langue
(`Accept-Language`).

## Exercice
