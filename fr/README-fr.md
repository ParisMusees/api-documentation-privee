# Documentation de l'API privée du portail des collections

Cette documentation complète la [documentation de l'API publique](https://github.com/ParisMusees/api-documentation)

## Utilisation d'une liste export
Une liste export peut contient des :
* Description
* Contenus (entity reference) vers :
    * Archive
    * Parcours Thématique
    * Ressource bibliographique
    * Oeuvre  
* Musée (valeur unique et requiert)

### Récupération des infos d'une liste export
```graphql
# Récupérations la descriptions, musée et les contenus de la liste avec id 89. 
# Aussi, récupération des titre, numero, description longue des oeuvres liés.
{
  listeExportById(id: "89") {
    ... on ListeExport {
      fieldDescription
      fieldMusee {
        targetId
        entity {
          ... on TaxonomyTermMusee {
            entityLabel
          }
        }
      }
      fieldExportApiContenus {
        targetId
        entity {
          entityId
          ... on NodeOeuvre {
            title
            fieldNumeroObjet
            fieldDescriptionLongueExport {
              value
              processed
            }
          }
        }
      }
    }
  }
}
```
```graphql
# Chargements la description courte du chaque contenu oeuvre de la liste export avec id 89, sur tous les langues disponibles.
{
  listeExportById(id: "89") {
    ... on ListeExport {
      fieldExportApiContenus {
        entity {
          entityId
          title
          entityTranslations {
            ... on NodeOeuvre {
              entityLanguage {
                name
              },
              fieldDescriptionCourteExport {
                value
                processed
              }
            }
          }
        }
      }
    }
  }
}
```

## Liste des champs privés
### Oeuvre
* fieldAuteurNotice
* fieldCommentaireHistorique
* fieldDescriptionCourteExport
* fieldDescriptionLongueExport
* fieldHdDescription
* fieldHdTitre
* fieldOeuvreGeolocalisation
* fieldReferenceExport

#### Exemple de requête avec des champs privés.
```graphql
# Les oeuvres du musée Cognacq Jay.
{
  nodeQuery(
    filter: {conditions:[
     {field: "field_musee.entity.field_lref_adlib", value: "5"}
     {field: "type", value: "oeuvre"}
    ]}
     sort: [
      # Tri par ordre chronologique sur la date de création
      {field: "created", direction: ASC}
      # Tri par ordre anté-chronologique sur la date de modification
      # {field: "changed", direction: DESC}
    ]
    # Limite la requête à 20 résultats
    limit: 20
    # Offset pour décaler les résultats obtenus en fonction du nombre entier renseigné.
    # offset: 20
  ) {
    count
    entities {
      ... on NodeOeuvre {
        title
        nid
        fieldVisuels {
          entity {
            name
            vignette
          }
        }
        fieldAuteurNotice {
          format
          value
          processed
        }
        fieldCommentaireHistorique {
          format
          value
          processed
        }
        fieldDescriptionCourteExport {
          format
          value
          processed
        }
        fieldDescriptionLongueExport {
          format
          value
          processed
        }
        fieldHdDescription {
          format
          value
          processed
        }
        fieldHdTitre
        fieldOeuvreGeolocalisation {
          lat
          lng
        }
        fieldReferenceExport {
          format
          value
          processed
        }
      }
    }
  }
}
```

### Media image
* fieldMediaImage

### Ressource bibliographique
* fieldRessNoteBibliographique

### Parcours thématique
* fieldAuteurNotice
* fieldDescriptionCourteExport
* fieldDescriptionLongueExport
* fieldReferenceExport

## Récupération des parcours thématiques

```graphql
# Récupération de 20 derniers parcours thématiqus et ses informations.
{
  nodeQuery(
    filter: {conditions:[
     {field: "type", value: "parcours_thematique"}
    ]}
     sort: [
      # Tri par ordre chronologique sur la date de création
      {field: "created", direction: ASC}
      # Tri par ordre anté-chronologique sur la date de modification
      # {field: "changed", direction: DESC}
    ]
    # Limite la requête à 20 résultats
    limit: 20
    # Offset pour décaler les résultats obtenus en fonction du nombre entier renseigné.
    # offset: 20
  ) {
    count
    entities {
      ... on NodeParcoursThematique {
        title
        nid
        fieldParcoursPresentation {
          value
        }
        fieldParcoursTimeline
        fieldMediaComplementaire {
          targetId
          entity {
            name
            vid
          }
        }
        fieldDescriptionCourteExport {
          format
          value
          processed
        }
        fieldReferenceExport {
          format
          value
          processed
        }
        fieldParcoursSections {
          entity {
            title
            fieldSectionSousTitre
            fieldSectionPresentation {
              value
            }
            fieldSectionVisuel {
              targetId
              entity {
                entityId
                thumbnail {
                  url
                  targetId
                  title
                }
                publicUrl
              }
            }
            fieldSectionGroupeOeuvres {
              targetId
              entity {
                fieldGroupeOeuvresTexte {
                  value
                }
                fieldGroupeOeuvre1Principale
                fieldOeuvresIncluses {
                  targetId
                  entity {
                    fieldLegende {
                      value
                    }
                    fieldOeuvre {
                      targetId
                      entity {
                        title
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```