```
graph TD
    A[ANTICIPATION] --> B{1. Freinage initial};
    B -- Léger --> B1(Pied droit freine doucement);
    B -- Dégressif --> B2(Lâche un peu le frein à mesure que la vitesse chute);
    
    B1 --> C{2. Embrayage};
    B2 --> C;
    C -- Basse vitesse <15km/h --> C1[Pied gauche appuie à FOND sur l'embrayage];
    C1 --> D(Continuer de doser le frein);
    D --> E[ARRÊT COMPLET];
    E --> F[Maintenir Frein & Embrayage];
```
