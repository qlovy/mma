# Tutoriel pour créer son programme en JSON

### But

Expliquer comment créer son programme de sport en JSON.

### Commençons

1. Pour modifier le fichier "model.json" ou autre, il est nécessaire d'avoir un éditeur de code
    <br>Je te conseille deux options :
   - **Visual Studio Code :** C'est un éditeur de code très populaire, si ce n'est le plus populaire. Il est simple à utiliser et intiutif. Pour le télécharger, clique sur ce [lien](https://code.visualstudio.com/).
   - **Utilise un éditeur en ligne :** Un editeur de JSON en ligne est une solution rapide, il en existe plein mais je recommande [lui](https://jsoneditoronline.org/).
<br><br>
2. Une fois le moyen pour éditer trouver et près à l'emploi, il est temps de passer à la modification. Cependant, il a quelques subtilités à prendre en compte.
<br><br>
3. La structure du fichier est exposée ci-dessous. Les `[]` contiennent le programme entier. Les `{}` contiennent une séance, soit un jour. 
Dedans, on retrouve toutes informations liées à une séance de sport : nom, jour et exercices. Les informations sont stockées avec un format clé-valeur. 
C'est-à-dire qu'une clé correspond à une valeur. Exemple : la clé `"nom"` désigne la valeur `"nom de l'exercice`. Pour la clé `"exercices"`, elle désigne un tableau à deux dimensions.
<br>
    ```
    [
      {
        "nom": "nom de l'exercice",
        "jour": "jour de l'exercice",
        "exercices": [
          ["nom exercice 1", 1, "15s", 1, "Conseil pour l'exercice"],
          ["nom exercice 2", 1, 5, 1, "Conseil pour l'exercice"]
        ]
      },
      {
        "nom": "nom de l'exercice",
        "jour": "jour de l'exercice",
        "exercices": [
          ["nom exercice 1", 1, "15s", 1, "Conseil pour l'exercice"],
          ["nom exercice 2", 1, 5, 1, "Conseil pour l'exercice"]
        ]
      }
    ]
    ```
4. Ainsi pour personnaliser le fichier, il suffit de remplacer la valeur de la clé. 
Une dernière précision, l'enregistrement d'un exercice se fait sous cette forme `["nom de l'exercice", nombre de série, nombre de répetitions, temps de récupération entre série en seconde, conseil pour la bonne exécution de l'exercice]`. 
<br>Exemple : `["Pompes", 4, 15, 30, "Enagager les abdos, les coudes le long du corps, ne pas toucher le sol avec le torse"]`.