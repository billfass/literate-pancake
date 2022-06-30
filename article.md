---
title: |
  "Introduction to Python modules."
date: May, 2022
lang: en-EN
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
## introduction
En python, les descripteurs sont des méthodes d'un objet qui permettent de protéger un attribut des modifications ou pour mettre à jour automatiquement les valeurs d'un attribut dépendant. Ils sont introduits à partir de Python 2.2. Leur utilisation permettent surtout d'améliorer la qualité du code Python, car à la base, Python n'a pas de concept de variables privées et les descripteurs sont considéré comme un moyen d'obtenir quelque chose de similaire.

## les différentes méthodes de descripteurs
Aussi appélés les setters et les getters, les descripteurs sont au nombres de trois. Il y a __get__, __set__ et __delete__.
Ce sont des attributs d'objet avec un comportement de liaison, dont l'accès à l'attribut est remplacé par les instructions du descripteur. Le comportement par défaut pour l'accès aux attributs consiste à obtenir, définir ou supprimer l'attribut du dictionnaire d'un objet. Si l'une de ces méthodes est définie pour un objet, on dit qu'il s'agit d'un descripteur.

## les types de descripteurs
On distingue en général deux types de descripteurs à savoir le descripteur de données et le descripteur non-data. Si un objet définit à la fois __get__ et __set__, il est considéré comme un descripteur de données. Les descripteurs qui ne définissent que __get__ sont appelés descripteurs non-data. Les descripteurs de données et les descripteurs non-data diffèrent dans la façon dont les dérogations sont calculées en ce qui concerne les entrées du dictionnaire d'une instance. Si le dictionnaire d'une instance comporte une entrée portant le même nom qu'un descripteur de données, le descripteur de données est prioritaire. Si le dictionnaire d'une instance comporte une entrée portant le même nom qu'un descripteur non-data, l'entrée du dictionnaire a la priorité.

## Invocation des descripteurs
Supposons que nous avons une classe personne avec deux attributs comme le nom et le prénom

```python
class Personne:
    def __init__(self, nom, prenom):
        self.nom = nom
        self.prenom = prenom
```
Dans cette classe, on souhaite avoir par exemple les attributs nom et prenom comme des des chaines de caractères non vide. On pourrait, par exemple, utiliser les setter et les getter pour appliquer ces contrôles comme ceci

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


