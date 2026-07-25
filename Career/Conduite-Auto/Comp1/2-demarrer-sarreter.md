```
sequenceDiagram
    participant Conducteur
    participant Pédale_Embrayage
    participant Moteur
    participant Roues

    Note over Conducteur: Préparation: Frein appuyé, Vitesse 1 engagée.
    Conducteur->>Pédale_Embrayage: Relève LENTEMENT la pédale
    Pédale_Embrayage-->>Moteur: Les disques d'embrayage s'approchent
    Moteur-->>Conducteur: VIBRATIONS LÉGÈRES (Point de Patinage !)
    Note right of Conducteur: C'est ICI qu'il faut bloquer le pied !
    Conducteur->>Roues: Relâche le FREIN
    Conducteur->>Pédale_Embrayage: Maintient la position 2-3 secondes
    Moteur->>Roues: La voiture commence à avancer doucement
    Conducteur->>Pédale_Embrayage: Relève COMPLÈTEMENT la pédale
    Note over Moteur, Roues: La voiture roule en 1ère.
```
