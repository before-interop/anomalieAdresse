# API AnomalieAdresse

Cet outil a été créé afin de respecter l’obligation réglementaire indiquée dans la [Décision Arcep n° 2020-1432].

Il permet le traitement d’une demande de création ou de modification d’adresses dans les IPE grâce à des flux normalisés.

## Swagger

Le swagger est disponible à l'adresse suivante : https://before-interop.github.io/anomalieAdresse/

TODO: adresse temporaire en attendant le Pull Request sur le repo officiel: https://ggrebert.github.io/anomalieAdresse

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


## Exemples d'utilisation de l'API

### Création d'une anomalie

```bash
curl -XPOST -d '{
  "@type": "AnomalieAdresseUpdateImb",
  "externalId": "123456789",
  "code_oi": "FTEL",
  "code_oc": "FTEL",
  "refs": [
    {
      "codeImb": "ID-75120000012C",
    },
    {
      "codeImb": "ID-7512000000H9",
    }
  ],
  "issues": [
    {
      "key": "address.postcode",
      "value": "75120"
    },
    {
      "key": "address.city",
      "description": "Ville inconnue"
    }
  ]
}' https://localhost/api/anomalie-adresse

HTTP/1.1 201 Created
ETag: 123456789abcdef
Location: /api/anomalie-adresse/330e4b53-e426-4ac3-a4c2-1f9f9d875b6a

{
  "id": "330e4b53-e426-4ac3-a4c2-1f9f9d875b6a",
  "externalId": "123456789",
  "code_oi": "FTEL",
  "code_oc": "FTEL",
  "refs": [
    {
      "codeImb": "ID-75120000012C",
    },
    {
      "codeImb": "ID-7512000000H9",
    }
  ],
  "issues": [
    {
      "key": "address.postcode",
      "value": "75120"
    },
    {
      "key": "address.city",
      "description": "Ville inconnue"
    }
  ],
  "status": "ACKNOWLEDGED",
  "statusChangeReason": null,
  "statusChangeDetails": null,
  "creationDate": "2021-01-01T00:00:00Z",
  "lastUpdateDate": "2021-01-01T00:00:00Z",
  "statusChange": [
    {
      "status": "ACKNOWLEDGED",
      "date": "2021-01-01T00:00:00Z"
    }
  ],
  "severity": "LOW",
  "ticketType": "OC_REQUEST",
  "analysisDelay": null,
  "resolutionDelay": null,
  "@type": "AnomalieAdresseUpdateImb",
  "@baseType": "AnomalieAdresse",
  "@version": "123456789abcdef",
  "href": "/api/anomalie-adresse/330e4b53-e426-4ac3-a4c2-1f9f9d875b6a"
}
```

### Refuser la résolution d'une anomalie

```bash
curl -H "If-Match: 234567890abcdef" -XPUT -d '{
  "status": "IN_PROGRESS",
  "statusChangeReason": "NOT_ACCEPTED",
  "statusChangeDetails": "Ville toujours inconnue",
}' https://localhost/api/anomalie-adresse/330e4b53-e426-4ac3-a4c2-1f9f9d875b6a

HTTP/1.1 200 OK
ETag: 345678901abcdef

{
  "id": "330e4b53-e426-4ac3-a4c2-1f9f9d875b6a",
  "externalId": "123456789",
  "code_oi": "FTEL",
  "code_oc": "FTEL",
  "refs": [
    {
      "codeImb": "ID-75120000012C",
    },
    {
      "codeImb": "ID-7512000000H9",
    }
  ],
  "issues": [
    {
      "key": "address.postcode",
      "value": "75120"
    },
    {
      "key": "address.city",
      "description": "Ville inconnue"
    }
  ],
  "status": "IN_PROGRESS",
  "statusChangeReason": "NOT_ACCEPTED",
  "statusChangeDetails": "Ville toujours inconnue",
  "creationDate": "2021-01-01T00:00:00Z",
  "lastUpdateDate": "2021-01-04T00:00:00Z",
  "statusChange": [
    {
      "status": "ACKNOWLEDGED",
      "date": "2021-01-01T00:00:00Z"
    },
    {
      "status": "IN_PROGRESS",
      "date": "2021-01-02T00:00:00Z"
    },
    {
      "status": "RESOLVED",
      "date": "2021-01-03T00:00:00Z"
    },
    {
      "status": "IN_PROGRESS",
      "date": "2021-01-04T00:00:00Z"
    }
  ],
  "severity": "LOW",
  "ticketType": "OC_REQUEST",
  "analysisDelay": 172800,
  "resolutionDelay": 259200,
  "@type": "AnomalieAdresseUpdateImb",
  "@baseType": "AnomalieAdresse",
  "@version": "345678901abcdef",
  "href": "/api/anomalie-adresse/330e4b53-e426-4ac3-a4c2-1f9f9d875b6a"
}
```

### Récupération des annomalies `PENDING`

```bash
curl "https://localhost/api/anomalie-adresse?status=PENDING"
```

### Récupération des annomalies résolue en plus de 8 semaines

```bash
curl "https://localhost/api/anomalie-adresse?status=RESOLVED&resolutionDelay[gt]=4838400"
```

### Lister les anomalies d'un immeuble spécifique de type `AnomalieAdresseUpdateImb`

```bash
curl "https://localhost/api/anomalie-adresse?%40type=AnomalieAdresseUpdateImb&refs.codeImb=ID-75120000012C"
```

### Lister les tickets en cours car la résolution n'a pas été acceptée

```bash
curl "https://localhost/api/anomalie-adresse?status=IN_PROGRESS&statusChangeReason=NOT_ACCEPTED"
```
