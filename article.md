---
title: |
  "Introduction to Python modules."
date: July, 2022
lang: fr-FR
urlcolor: blue
geometry: "left=2.5cm,right=2.5cm,top=3cm,bottom=3cm"
documentclass: article
fontfamily: Alegreya
header-includes: |
    \usepackage{fancyhdr}
    \pagestyle{fancy}
    \fancyhf{}
    \rhead{Dakar Institute of Technology}
    \lhead{Bile Fassinou}
    \rfoot{Page \thepage}
    \hypersetup{pdftex,
            pdfauthor={Bile Fassinou},
            pdftitle={Introduction to Python programming},
            pdfsubject={Introduction to Python programming},
            pdfkeywords={Python, Programming},
            pdfproducer={Emacs, Pandoc, Latex, Markdown},
            pdfcreator={Emacs, Pandoc, Latex, Markdown}}
    
---

# Descriptors
## Introduction
En python, les descripteurs sont des classes contenant certaines méthodes spécifiques qui permettent de protéger un attribut des modifications ou pour mettre à jour automatiquement les valeurs d'un attribut dépendant. Ils sont introduits à partir de Python 2.2. Leur utilisation permettent surtout d'améliorer la qualité du code Python, car à la base, Python n'a pas de concept de variables privées et les descripteurs sont considéré comme un moyen d'obtenir quelque chose de similaire. Les méthodes de ces classes s'appliquent seulement quand une instance de la classe (dite classe descripteur) contenant la méthode apparaît dans une classe propriétaire (owner) ; la classe descripteur doit figurer dans le dictionnaire de la classe propriétaire ou dans le dictionnaire de la classe d'un des parents. 

## Les différentes méthodes qui font d'une classe un descripteur
Ces méthodes sont au nombres de trois. Il y a \_\_get\_\_, \_\_set\_\_ et \_\_delete\_\_.
Ce sont des attributs d'objet avec un comportement de liaison, dont l'accès à l'attribut est remplacé par les instructions du descripteur. Le comportement par défaut pour l'accès aux attributs consiste à obtenir, définir ou supprimer l'attribut du dictionnaire d'un objet. Si l'une de ces méthodes est définie pour un objet ou classe, on dit qu'il s'agit d'un descripteur.

### \_\_get\_\_
Cette méthode est appelée pour obtenir l'attribut de la classe propriétaire ou d'une instance de cette classe. 

```python
# exemple de méthode __get__
def __get__(self, instance, owner):
  return self 
```
owner est toujours de la classe propriétaire alors que instance est l'instance par laquelle on accède à l'attribut ou None lorsque l'on accède par la classe owner. Cette méthode doit renvoyer la valeur (calculée) de l'attribut ou lever une exception 

### \_\_set\_\_
Cette méthode est appelée pour définir l'attribut d'une instance de la classe propriétaire à la nouvelle valeur value.

```python
# exemple de méthode __set__
def __set__(self, instance, owner):
  if not isinstance(value, str):
    raise ValueError(f"Le {self._name} doit être un string")

  if len(value) == 0:
    raise ValueError(f"Le {self._name} ne pas être vide")
```

### \_\_delete\_\_
Cette méthode est appelée pour supprimer l'attribut de l'instance instance de la classe propriétaire.

```python
def __delete__(self, instance):
  pass

```

## les types de descripteurs
On distingue en général deux types de descripteurs à savoir le descripteur data et le descripteur non-data. Si un objet ou une classe définit à la fois \_\_get\_\_ et \_\_set\_\_, il est considéré comme un descripteur data. Les classes qui ne définissent que \_\_get\_\_ sont appelés descripteurs non-data. 

```python
# un exemple de descripteur data
class DataDescriptor : 
    def __set_name__ (self, owner, name): 
        self.name = name 

    def __get__ (self): 
        return self.name 

    def __set__ (self, value): 
        self.name = value
```

```python
# un exemple de descripteur de non-data
class NonDataDescriptor : 
    def __set_name__(self, owner, name): 
        self.name = name 

    def __get__ (self): 
        return self.name 
```
Les descripteurs data et les descripteurs non-data diffèrent dans la façon dont les dérogations sont calculées en ce qui concerne les entrées du dictionnaire d'une instance. Si le dictionnaire d'une instance comporte une entrée portant le même nom qu'un descripteur data, le descripteur data est prioritaire. Si le dictionnaire d'une instance comporte une entrée portant le même nom qu'un descripteur non-data, l'entrée du dictionnaire a la priorité.

## Cas d'utilisation des descripteurs
Supposons que nous avons une classe personne avec deux attributs comme le nom et le prénom

```python
class Personne:
    def __init__(self, nom, prenom):
        self.nom = nom
        self.prenom = prenom
```
Dans cette classe, on souhaite avoir par exemple les attributs nom et prenom comme des chaines de caractères non vide. On pourrait, par exemple, utiliser les setter et les getter pour appliquer ces contrôles comme ceci

```python
class Personne:
    def __init__(self, nom, prenom):
        self.nom = nom
        self.prenom = prenom

    @property
    def prenom(self):
        return self._prenom

    @prenom.setter
    def prenom(self, value):
        if not isinstance(value, str):
            raise ValueError('Le prénom doit être un string')

        if len(value) == 0:
            raise ValueError('Le prénom ne peut pas être vide')

        self._prenom = value

    @property
    def nom(self):
        return self._nom

    @nom.setter
    def nom(self, value):
        if not isinstance(value, str):
            raise ValueError('Le nom doit être un string')

        if len(value) == 0:
            raise ValueError('Le nom ne peut pas être vide')

        self._nom = value
```
Dans le code ci-dessous, nous avons la classe Personne qui a un getter qui renvoie la valeur de l'attribut tandis que le setter la valide avant de l'affecter à l'attribut. Et il fonctionne très bien. 

Si je le teste en faisant par exemple ceci : 

```python
try:
    personne = Personne("","")
except ValueError as e:
    print(e)
```
Il me retourne très bien le message d'erreur :

> Le nom ne peut pas être vide

Maintenant, si je le teste en mettant les valeurs correctes, il fonctionne aussi bien (l'exemple dans le code ci-dessous).

```python
try:
    personne = Personne("Fassinou","Bile")
except ValueError as e:
    print(e)
print(f"Bonjour, je mappelle {personne.prenom} {personne.nom}")
```

Ce code fonctionne, toutefois, il est redondant. C'est la même logique de validation qui valide les attributs. Ce code est parfait pour deux attributs, mais si la classe devrait en avoir assez qui nécessite la même logique de validation, on est alors obligé de dupliquer dans d'autres propriétés. Il n'est donc pas réutilisable. 

Pour éviter de dupliquer la logique, nous pouvons avoir une méthode qui valide les données et réutiliser cette méthode dans d'autres propriétés. Cette approche permettra la réutilisation. Cependant, Python a une meilleure façon de résoudre ce problème en utilisant des descripteurs. D'abord, commençons par définir une classe descripteur qui implémente trois méthodes : \_\_set_name\_\_, \_\_get\_\_ et \_\_set\_\_.

```python
class RequiredString:
    def __set_name__(self, owner, name):
        self._name = name

    def __get__(self, instance, owner):
        if instance is None:
            return self

        return instance.__dict__[self._name] or None

    def __set__(self, instance, value):
        if not isinstance(value, str):
            raise ValueError(f"Le {self._name} doit être un string")

        if len(value) == 0:
            raise ValueError(f"Le {self._name} ne pas être vide")

        instance.__dict__[self._name] = value
```
Ensuite, utilisons le descripteur RequiredString dans la classe Person :

```python
class Personne:
    prenom = RequiredString()
    nom = RequiredString()
```

Si on affecte une chaîne vide ou une valeur non-chaîne à l'attribut prenom, on obtient une erreur. 

```python
try:
    personne = Personne()
    personne.prenom = ''
except ValueError as e:
    print(e)
```

La bonne nouvelle ici est que nous pouvons utiliser notre descripteur RequiredString dans n'importe quelle classe avec des attributs nécessitant une valeur de chaîne non vide, c'est-à-dire les mêmes conditions de vérifications. Aussi, nous pouvons définir d'autres descripteurs pour appliquer d'autres types de données comme l'âge, l'e-mail et le téléphone. Je rappelle que ceci n'est qu'un simple des cas d'utilisation des descripteurs.

Amusons-nous par exemple pour définir un descripteur pour contrôler l'email avec la bibliothèque d'expression régulière re.

```python
import re 
class RequiredEmail:
    def __set_name__(self, owner, email):
        self._email = email

    def __get__(self, instance, owner):
        if instance is None:
            return self

        return instance.__dict__[self._email] or None

    def __set__(self, instance, value):
        regex = '^[a-z0-9]+[\._]?[a-z0-9]+[@]\w+[.]\w{2,3}$'
        if(re.search(regex,value)):  
            print("Votre email est valide")    
        else:  
            print("Désolé, votre email n'est pas valide")

        instance.__dict__[self._email] = value
```

```python
class checkEmail:
    email = RequiredEmail()
```

```python
try:
    myEmail = checkEmail()
    myEmail.email = 'billfass2010@gmail.com'
except ValueError as e:
    print(e)
```

Faisons quand même un rappel sur la méthode \_\_set_name\_\_. Cette méthode magique a été incluse à partir de Python 3.6, qui est automatiquement appelée lors de la création d'une classe, et elle reçoit deux paramètres : la classe et le nom de l'attribut tel qu'il apparaît défini dans la classe.

```python
def __set_name__(self, owner, name):
    self.name = name
```
Et voilà ! Nous sommes à la fin de cet article et espérons que vous avez aimé. Nous restons ouvert pour toute correction, contribution et suggestion. 