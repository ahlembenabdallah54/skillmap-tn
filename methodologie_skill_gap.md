# Méthodologie d'analyse du skill gap

**SKILLMAP TN : Analyse des écarts de compétences et recommandations**

## Objectif

Comparer les compétences déclarées par un étudiant à celles demandées dans les offres d'emploi du métier qu'il vise, puis lui recommander quelles compétences apprendre en priorité. Chaque recommandation est justifiée par des chiffres issus du corpus d'annonces.

## 1. Représenter le profil de l'utilisateur

Le profil contient deux éléments : le **métier cible** et la **liste des compétences déclarées** par l'étudiant.

```python
profil = {"metier_cible": "Data & BI", "competences": ["Python", "SQL", "Pandas"]}
```

On utilise uniquement les compétences **déclarées**, sans jamais en deviner d'autres. Si l'étudiant n'a pas écrit « NumPy », on ne suppose pas qu'il le connaît, même si c'est probable. Sinon, on pourrait lui cacher une compétence qu'il doit réellement apprendre (règle du cahier des charges : « aucune maîtrise inférée »).

## 2. Représenter les exigences d'un poste

Les exigences d'un métier sont les compétences extraites des annonces de la catégorie correspondante (par exemple « Data & BI »). Chaque annonce a une catégorie et une liste de compétences. L'extraction vient du module NLP (Membre 2) et la catégorie du module de classification (Membre 3).

## 3. Matching entre compétences

On compare la liste du profil aux compétences observées dans les annonces de la catégorie visée. La comparaison se fait sur des noms **canoniques** : la taxonomie du Membre 2 regroupe les synonymes (ML, Machine Learning, apprentissage automatique) pour qu'ils comptent comme une seule compétence.

## 4. Calcul du skill gap

1. Sélectionner les annonces de la catégorie visée.
2. Compter, pour chaque compétence, le nombre d'annonces qui la mentionnent.
3. **Retirer** les compétences déjà déclarées par l'étudiant : ce qui reste est le gap.
4. Calculer la demande de chaque compétence manquante :

> **Demande(s, c) = nombre d'annonces de la catégorie c mentionnant s ÷ nombre total d'annonces analysées de c**

La demande est toujours affichée avec son numérateur et son dénominateur (par exemple 5/10), car c'est une proportion observée dans le corpus et non une mesure du marché entier. Tous les calculs sont faits par du **code déterministe**, jamais par le LLM.

## 5. Priorisation et recommandations

Les compétences manquantes sont classées par **demande décroissante** : plus une compétence apparaît souvent dans les annonces du métier visé, plus elle est prioritaire. En cas d'égalité, on classe par ordre alphabétique. Chaque recommandation affiche la compétence, le nombre d'annonces, le total et la catégorie. Les critères suivants pourront compléter ce classement dans les sprints suivants : la diversité d'apparition des compétences et le niveau de l'étudiant.

## Exemple (données fictives du prototype)

Profil : métier cible **Data & BI**, compétences déclarées **Python, SQL, Pandas**. Corpus : 10 annonces Data & BI.

| Priorité | Compétence | Annonces | Total | Demande |
|---|---|---|---|---|
| 1 | Power BI | 5 | 10 | 50 % |
| 2 | Machine Learning | 4 | 10 | 40 % |
| 3 | Excel | 2 | 10 | 20 % |
| 4 | Tableau | 2 | 10 | 20 % |
| 5 | Deep Learning | 1 | 10 | 10 % |

**Recommandation :** apprendre d'abord Power BI (présent dans 5 annonces sur 10), puis Machine Learning (4 sur 10).

**Cas limite :** si aucune annonce n'existe pour la catégorie demandée, le système répond « Données insuffisantes » au lieu d'inventer un résultat.

## Limites

- Les chiffres de l'exemple sont fictifs ; ils seront remplacés par le vrai corpus.
- Le corpus ne représente pas nécessairement tout le marché tunisien.
- Une compétence absente des annonces n'est pas forcément inutile.
- Les recommandations indiquent des tendances observées, sans promesse d'embauche : l'étudiant reste décisionnaire.
