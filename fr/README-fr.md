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

Exemple de requête avec des champs privés :
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

Exemple de requête avec des champs privés :
```graphql
# Liste des images libre de droit d'une oeuvre.
{
  nodeById(id: "128906") {
    ... on NodeOeuvre {
      title
      queryFieldVisuels(filter: {conditions: [
        {field: "field_image_libre", value: "1"}
      ]}) {
        entities {
          ... on MediaImage {
            name
            vignette
            publicUrl
          }
        }
      }
    }
  }
}
```

### Media audio
* fieldMediaFile
* fieldPrivate

Exemple de requête avec des champs privés :
```graphql
# Récupérer tous les audios privé.
{
  mediaQuery(filter: { conditions: [{operator: EQUAL, field: "field_private", value: "1"}]}) {
    entities {
      ... on MediaAudio {
        entityId
        name
        fieldPrivate
        fieldMediaFile {
          entity {
            fid
            url
            filename
            filesize
            filemime
            fieldLegende
          }
        }
        # plus des champs si besoin.
      }
    }
  }
}
```

```graphql
# Récupérer tous les traductions pour le média audio avec id 1643285.
{
  mediaById (id: "1643285") {
    entityTranslations {
      ... on MediaAudio {
        entityId
        entityLanguage {
            name
        }
        name
        fieldMusee {
          targetId
          entity {
            name
          }
        }
        fieldMediaFile{
          targetId
          entity {
            url
            filename
          }
        }
        fieldCopyright
        fieldLegende
        fieldPrivate
        fieldMediaTags {
          entity {
            entityId
            name
          }
        }
      }
    }
  }
}
```

```graphql
#Récuperer les traductions qui nont pas de fichier audio.
{
  mediaQuery (
    filter: {conditions:[
      {field: "bundle", value: "audio"}
      {field: "field_media_file", operator: IS_NULL}
      {field: "langcode", operator: NOT_EQUAL, value: "fr"}
    ]}
   sort: [
      {field: "created", direction: DESC}
    ]
  ) {
    count
    entities {
      entityTranslations {
        ... on MediaAudio {
          entityId
          entityLanguage {
              name
          }
          name
        }
      }
    }
  }
}
```

### Ressource bibliographique
* fieldRessNoteBibliographique

### Parcours thématique
* fieldAuteurNotice
* fieldDescriptionCourteExport
* fieldDescriptionLongueExport
* fieldReferenceExport

Récupération des parcours thématiques :

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