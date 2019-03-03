## Variable

<http://php.net/manual/fr/language.variables.php>

En php, pour utiliser les variables nous allons utiliser devant chaque nom de variable $


```php
$variable = 'Chaine de caractère';
echo $variable;
```
Résultat :

```
Chaine de caractère
```
---

```php
$variable = 1;
$variable++;
echo $variale;
```
Résultat :

```
2
```
---
<http://php.net/manual/fr/language.types.php>

Si nous le souhaitons, nous pouvons typer nos variables:

```php
$variable = (int) 1;
echo $variale;
```
Résultat :

```
1
```

## Tableau

<http://php.net/manual/fr/language.types.array.php>

Pour déclarer un tableau en php, il y a deux manières, l'ancienne avec array(), et la nouveau avec [].

Pour parcourir un tableau nous allons utilise la fonction php `foreach`
<http://php.net/manual/fr/control-structures.foreach.php>

```php
$array = ['Liens 1', 'Liens 2', 'Liens 3];
foreach($array as $value) {
	echo $value . '<br>';
}

```
Résultat :

```
Liens 1
Liens 2
Liens 3
```
---

Sur l'exemple plus haut par defaut les clés des tableaux va aller de 0 à 2

```php
$array = ['Liens 1', 'Liens 2', 'Liens 3];
echo $array[0] . '<br>';
echo $array[1] . '<br>';
echo $array[2] . '<br>';


```
Résultat :

```
Liens 1
Liens 2
Liens 3
```

---

Nous ne sommes pas obliger de mettre des valeurs par defaut, nous pouvons définir les clés


```php
$array = ['liens1' => 'index.php', 'liens2' => 'presentation.php'];
foreach ($array as $key => $value){
    echo $key . ' - '. $value . '<br>';
}
```
Résultat :

```
liens1 - index.php
liens2 - presentation.php
```

---

Et nous pouvons combiner l'ensemble 

```php
$array[] = ['url' => 'index.php', name => 'Accueil'];
$array[] = ['url' => 'presentation.php', name => 'Présentation'];

foreach($array as $value) {
	echo $value['url'] . ' - ' . $value['name'] . '<br>';
}

```
Résultat :

```
index.php - Accueil
presentation.php - Présentation
```