# API AnomalieAdresse

Cet outil a été créé afin de respecter l’obligation réglementaire indiquée dans la [Décision Arcep n° 2020-1432].

Il permet le traitement d’une demande de création ou de modification d’adresses dans les IPE grâce à des flux normalisés.

## Swagger

Le swagger est disponible à l'adresse suivante : https://before-interop.github.io/anomalieAdresse/

## Types d'anomalies

![Types de signalement](./type.drawio.svg)

## Cycle de vie d'une anomalie

![Workflow](./status.drawio.svg)

## Cas d'utilisation

### cas nominal

```mermaid
sequenceDiagram

  participant OC
  participant OI

  OC->>OI: Création d'une anomalie (ACKNOWLEDGED)

  OI->>OI: Contrôles métier
  alt Contrôles OK
    OI->>OI: Passage en IN_PROGRESS
    OI->>OC: Event ticket_updated
    OI->>OI: Traitement de l'anomalie
    OI->>OI: Passage en RESOLVED
    OI->>OC: Event ticket_updated
    OC->>OI: Validation de la résolution
  else Contrôles KO
    OI-->>OI: Passage en REJECTED
    OI->>OC: Event ticket_updated
  end
```

### Gestion des pièces jointes

```mermaid
sequenceDiagram

  participant OC
  participant OI

  OC->>OI: Création d'une anomalie en status CREATING

  loop Pour chaque pièce jointe

    OC->>OI: Ajout d'une pièce jointe

  end

  OC->>OI: Passage en status ACKNOWLEDGED
```

[Décision Arcep n° 2020-1432]: https://www.arcep.fr/uploads/tx_gsavis/20-1432.pdf
