---
title: 'Les dates'
sitemap:
    lastmod: '22-08-2024 13:32'
partials:
    header_subtitle:
        toggle: true
    metadata:
        where: header
    breadcrumbs:
        toggle: false
feed:
    limit: 10
show_sidebar: false
process:
    markdown: true
    twig: false
content:
    items: '@self.listing'
---

# Les dates

Les dates et le temps sont représentés en Java par des classes. Cependant, au fil
des versions de l’API standard, de nouvelles classes ont été proposées pour représenter
les dates et le temps. Pour des raisons de compatibilité ascendante, les anciennes
classes sont demeurées même si une partie de leurs méthodes ont été dépréciées.
Ainsi, il existe plusieurs façons de représenter les dates et le temps en Java.

## Date : la classe historique

La classe [java.util.Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) est la première classe a être apparue pour représenter
une date. Elle comporte de nombreuses limitations :

* Il n’est pas possible de représenter des dates antérieures à 1900
* Elle ne supporte pas les fuseaux horaires
* Elle ne supporte que les dates pour le calendrier grégorien
* Elle ne permet pas d’effectuer des opérations (ajout d’un jour, d’une année…)

La quasi totalité des constructeurs et des méthodes de cette classe ont été
dépréciés. Cela signifie qu’il ne faut pas les utiliser. Pourtant, la classe [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html)
reste une classe largement utilisée en Java. Par exemple, la classe [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) est la
classe mère des classes [java.sql.Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/Date.html), [java.sql.Time](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/Time.html) et [java.sql.Timestamp](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/Timestamp.html) qui
servent à représenter les types du même nom en SQL. Beaucoup de bibliothèques
tierces utilisent directement ou indirectement la classe [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html).

```java
import java.time.Instant;
import java.util.Date;

public class TestDate {

  public static void main(String[] args) {
    Date aujourdhui = new Date(); // crée une date au jour et à l'heure d'aujourd'hui
    Date dateAvant = new Date(0); // crée une date au 1 janvier 1970 00:00:00.000

    System.out.println(aujourdhui.after(dateAvant)); // true
    System.out.println(dateAvant.before(aujourdhui)); // true

    System.out.println(aujourdhui.equals(dateAvant)); // false
    System.out.println(aujourdhui.compareTo(dateAvant)); // 1

    Instant instant = aujourdhui.toInstant();
  }

}
```

Actuellement, les méthodes permettant de comparer des instances de [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html)
ne sont pas dépréciées. Pour construire une instance de [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html), on peut utiliser
le constructeur sans paramètre pour créer une date au jour et à l’heure d’aujourd’hui.
On peut également passer un nombre de type **long** représentant le nombre de millisecondes
depuis le 1er janvier 1970 à 00:00:00.000 (*l’epoch*). Il est également possible de modifier
une instance de [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) avec la méthode [Date.setTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html#setTime-long-) en fournissant le nombre de
millisecondes depuis le 1er janvier 1970 à 00:00:00.000.

#### NOTE
Depuis l’introduction en Java 8 de la nouvelle API des dates,
il est possible de convertir une instance de [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) en une instance de [Instant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Instant.html) avec
la méthode [Date.toInstant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html#toInstant--).

## Calendar

Depuis Java 1.1, la classe [java.util.Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) a été proposée pour remplacer
la classe [java.util.Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html). La classe [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) pallie les nombreux désavantages
de la classe [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) :

* Il est possible de représenter toutes les dates (y compris avant notre ère)
* La classe [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) supporte les fuseaux horaires (*timezone*)
* La classe [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) est une classe abstraite pour laquelle il est possible de
  fournir des classes concrètes pour gérer différents calendriers (le JDK ne propose
  que la classe [GregorianCalendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/GregorianCalendar.html) comme implémentation concrète pour le calendrier grégorien).

```java
import java.util.Calendar;
import java.util.Locale;
import java.util.TimeZone;

public class TestCalendar {

  public static void main(String[] args) {
    // Date et heure d'aujourd'hui en utilisant le fuseau horaire du système
    Calendar date = Calendar.getInstance();
    // Date et heure d'aujourd'hui en utilisant le fuseau horaire de la France
    Calendar dateFrance = Calendar.getInstance(Locale.FRANCE);
    // Date et heure d'aujourd'hui en utilisant le fuseau horaire GMT
    Calendar dateGmt = Calendar.getInstance(TimeZone.getTimeZone("GMT"));

    // On positionne la date au 8 juin 2005 à 12:00:00
    date.set(2005, 6, 8, 12, 0, 0);

    System.out.println(date.toInstant());

    // On ajoute 24 heures à la date et on passe au jour suivant
    date.add(Calendar.HOUR, 24);
    // On décale la date de 12 mois sans passer à l'année suivante
    date.roll(Calendar.MONTH, 12);
    System.out.println(date.toInstant()); // 9 juin 2005 à 12:00:00

  }

}
```

Comme pour les instances de [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html), il est possible de comparer les instances
de [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) entre elles. Il est également possible de convertir une instance
de [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) en [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) (mais alors on perd l’information du fuseau horaire
puisque la classe [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) ne contient pas cette information) grâce à la méthode
[Calendar.getTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html#getTime--). Enfin, on utilise la méthode [Calendar.toInstant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html#toInstant--) pour convertir
une instance de [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) en une instance de [Instant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Instant.html).

Même si la classe [Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html) est beaucoup plus complète que la classe [Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html), son
utilisation est restée limitée car elle est également plus difficile à manipuler.
Son API la rend assez fastidieuse d’utilisation. Elle ne permet pas de représenter
simplement la notion du durée. Et surtout, comme il s’agit d’une classe abstraite,
il n’est pas possible construire une instance avec l’opérateur **new**. Il faut
systématiquement utiliser une des méthodes de classes [Calendar.getInstance](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html#getInstance--).

## L’API Date/Time

Depuis Java 8, une nouvelle API a été introduite pour représenter les dates, le
temps et la durée. Toutes ces classes ont été regroupées dans la package [java.time](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/package-summary.html).

### Les Dates

Les classes [LocalDate](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDate.html), [LocalTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalTime.html) et [LocalDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDateTime.html) permettent de représenter respectivement
une date, une heure, une date et une heure.

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.Month;
import java.time.temporal.ChronoUnit;

public class TestTime {

  public static void main(String[] args) {
    LocalDate date = LocalDate.of(2005, Month.JUNE, 5); // 05/06/2005
    date = date.plus(1, ChronoUnit.DAYS); // 06/06/2005
    LocalDateTime dateTime = date.atTime(12, 00); // 06/06/2005 12:00:00
    LocalTime time = dateTime.toLocalTime(); // 12:00:00

    time = time.minusHours(2); // 10:00:00
  }

}
```

On peut facilement passer d’un type à une autre. Par exemple la méthode
[LocalDate.atTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDate.html#atTime-int-int-) permet d’ajouter une heure à une date, créant ainsi une instance
de [LocalDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDateTime.html). Toutes les instances de ces classes sont immutables.

Si on veut avoir l’information de la date ou de l’heure d’aujourd’hui, on peut
créer une instance grâce à la méthode *now*.

```java
LocalDate dateAujourdhui = LocalDate.now();
LocalTime heureMaintenant = LocalTime.now();
LocalDateTime dateHeureMaintenant = LocalDateTime.now();
```

Une instance de ces classes ne contient pas d’information de fuseau horaire.
On peut néanmoins passer en paramètre des méthodes *now* un [ZoneId](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/ZoneId.html) pour indiquer
le fuseau horaire pour lequel on désire la date et/ou l’heure actuelle.

```java
LocalDate dateAujourdhui = LocalDate.now(ZoneId.of("GMT"));
LocalTime heureMaintenant = LocalTime.now(ZoneId.of("Europe/Paris"));
LocalDateTime dateHeureMaintenant = LocalDateTime.now(ZoneId.of("America/New_York"));
```

#### NOTE
Si vous avez besoin de représenter des dates avec le fuseau horaire, alors il faut
utiliser la classe [ZonedDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/ZonedDateTime.html).

Les classe [Year](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Year.html) et [YearMonth](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/YearMonth.html) permettent de manipuler les dates et d’obtenir
des informations intéressantes à partir de l’année ou du mois et de l’année.

```java
import java.time.LocalDate;
import java.time.Month;
import java.time.Year;
import java.time.YearMonth;

public class TestYear {

  public static void main(String[] args) {
    Year year = Year.of(2004);

    // année bissextile ?
    boolean isLeap = year.isLeap();

    // 08/2004
    YearMonth yearMonth = year.atMonth(Month.AUGUST);

    // 31/08/2004
    LocalDate localDate = yearMonth.atEndOfMonth();
  }

}
```

### La classe Instant

La classe [Instant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Instant.html) représente un point dans le temps. Contrairement aux classes
précédentes qui permettent de représenter les dates pour les humains, la classe
[Instant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Instant.html) est adaptée pour réaliser des traitements de données temporelles.

```java
import java.time.Instant;

public class TestInstant {

  public static void main(String[] args) {
    Instant maintenant = Instant.now();
    Instant epoch = Instant.ofEpochSecond(0); // 01/01/1970 00:00:00.000

    Instant uneMinuteDansLeFuture = maintenant.plusSeconds(60);

    long unixTimestamp = uneMinuteDansLeFuture.getEpochSecond();
  }

}
```

#### NOTE
Les classes [LocalDate](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDate.html), [LocalTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalTime.html), [LocalDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDateTime.html), [ZonedDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/ZonedDateTime.html), [Year](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Year.html), [YearMonth](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/YearMonth.html),
[Instant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Instant.html) implémentent toutes les interfaces [Temporal](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/temporal/Temporal.html) et [TemporalAccessor](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/temporal/TemporalAccessor.html). Cela
permet d’utiliser facilement des instances de ces classes les unes avec les autres
puisque beaucoup de leurs méthodes attendent en paramètres des instances de type
[Temporal](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/temporal/Temporal.html) ou [TemporalAccessor](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/temporal/TemporalAccessor.html).

### Période et durée

Il est possible de définir des périodes grâce à des instances de la classe [Period](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Period.html).
Une période peut être construite directement ou à partir de la différence entre deux
instances de type [Temporal](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/temporal/Temporal.html). Il est ensuite possible de modifier une date en ajoutant ou soustrayant
une période.

```java
import java.time.LocalDate;
import java.time.Month;
import java.time.Period;
import java.time.Year;
import java.time.YearMonth;

public class TestPeriode {

  public static void main(String[] args) {
    YearMonth moisAnnee = Year.of(2000).atMonth(Month.APRIL); // 04/2000

    // période de 1 an et deux mois
    Period periode = Period.ofYears(1).plusMonths(2);

    YearMonth moisAnneePlusTard = moisAnnee.plus(periode); // 06/2001

    Period periode65Jours = Period.between(LocalDate.now(), LocalDate.now().plusDays(65));
  }

}
```

La durée est représentée par une instance de la classe [Duration](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Duration.html). Elle peut être
obtenue à partir de deux instances de [Instant](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Instant.html).

```java
import java.time.Duration;
import java.time.Instant;

public class TestDuree {

  public static void main(String[] args) {
    Instant debut = Instant.now();

    // ... traitement à mesurer

    Duration duree = Duration.between(debut, Instant.now());
    System.out.println(duree.toMillis());
  }

}
```

## Formatage des dates

Pour formater une date pour l’affichage, il est possible d’utiliser la méthode
*format* déclarée dans les classes [LocalDate](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDate.html), [LocalTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalTime.html), [LocalDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/LocalDateTime.html),
[ZonedDateTime](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/ZonedDateTime.html), [Year](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/Year.html) et [YearMonth](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/YearMonth.html).

Le format de représentation d’une date et/ou du temps est défini par la classe
[DateTimeFormatter](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/time/format/DateTimeFormatter.html).

```java
import java.time.LocalDateTime;
import java.time.Month;
import java.time.format.DateTimeFormatter;
import java.util.Locale;

public class TestDuree {

  public static void main(String[] args) {
    // 01/09/2010 16:30
    LocalDateTime dateTime = LocalDateTime.of(2010, Month.SEPTEMBER, 1, 16, 30);

    // En utilisant des formats ISO de dates
    System.out.println(dateTime.format(DateTimeFormatter.BASIC_ISO_DATE));
    System.out.println(dateTime.format(DateTimeFormatter.ISO_WEEK_DATE));
    System.out.println(dateTime.format(DateTimeFormatter.ISO_DATE_TIME));

    DateTimeFormatter datePattern = DateTimeFormatter.ofPattern("dd/MM/yyyy");
    // 01/09/2010
    System.out.println(dateTime.format(datePattern));

    DateTimeFormatter dateTimePattern = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");
    // 01/09/2010 16:30
    System.out.println(dateTime.format(dateTimePattern));

    // 1 septembre 2010
    DateTimeFormatter frenchDatePattern = DateTimeFormatter.ofPattern("d MMMM yyyy", Locale.FRANCE);
    System.out.println(dateTime.format(frenchDatePattern));
  }

}
```

#### NOTE
Il est toujours possible d’utiliser la classe [SimpleDateFormat](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/text/SimpleDateFormat.html) pour formater
une instance de la classe [java.util.Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html).
