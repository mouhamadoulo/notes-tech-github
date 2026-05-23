# Guide des éléments de syntaxe Markdown

## 1. Titres
```md
# Titre niveau 1
## Titre niveau 2
### Titre niveau 3
#### Titre niveau 4
##### Titre niveau 5
###### Titre niveau 6
```

## 2. Texte
```md
*italique* ou _italique_  
**gras** ou __gras__  
***gras et italique***  
~~texte barré~~
```

## 3. Listes

### Liste non ordonnée
```md
- Élément
- Élément
  - Sous-élément
* Élément
+ Élément
```

### Liste ordonnée
```md
1. Premier
2. Deuxième
   1. Sous-élément
```

## 4. Liens
```md
[Texte du lien](https://exemple.com)
```

## 5. Images
```md
![Texte alternatif](https://exemple.com/image.png)
```

## 6. Citations
```md
> Ceci est une citation
>> Citation imbriquée
```

## 7. Code

### Code en ligne
```md
Voici du `code en ligne`
```

### Bloc de code
```python
print("Hello world")
```

## 8. Séparateurs
```md
---
***
___
```

## 9. Tableaux
```md
| Colonne 1 | Colonne 2 |
|----------|----------|
| Valeur 1 | Valeur 2 |
| Valeur 3 | Valeur 4 |
```

## 10. Cases à cocher
```md
- [x] Tâche faite
- [ ] Tâche à faire
```

## 11. Échappement de caractères
```md
\*Ce texte n'est pas en italique\*
```

## 12. HTML (optionnel)
```md
<b>Texte en gras HTML</b>
```

## 13. Ancres internes
```md
[Titre](#titre)
```

## 14. Notes de bas de page (selon support)
```md
Texte avec note[^1]

[^1]: Ceci est une note
```