# Django-Sagenkarta-Elasticsearch-demo

Enkelt Django application som visar hur man kan göra enkel API som hämtar data från Elasticserach utan att definera models.
Django samt requests modulen måste vara installerad för att köra applicationen.
Applicationen startas via `python manage.py runserver`.

Koden för API:et finns i `sagenkarta_api/urls.py` och `sagenkarta_api/views.py`

## Hämta documenter
`[HOST]/sagenkarta/documents/`. Enkel sökning kan utföras med `[HOST]/sagenkarta/documents/**?search=häst**`

## Hämta enda dokument
`[HOST]/sagenkarta/document/[DOC_ID]`

## sagenkarta_es_api

### createQuery
`def createQuery(request)`

Tar inn request object från Django och bygger upp Elasticsearch query baserad på querysträng från URL:et. Levererar Elasticsearch query som dictionary.

Giltiga params:
- **collection_years=[från,till]**
Hämtar documenter var `year` är mellan från och till. Exempel: `collection_year=1900,1910`
- **search=[söksträng]**
Hämtar documenter var ett eller flera eller alla ord förekommer i titel eller text. Exempel: (ett eller flera ord) `search=svart hund`, (alla ord) `search=svart,hund`
- **category=[kategori bokstav]**
Hämtar documenter som finns i angiven kategori (en eller flera). Exempel: `category=L,H`
- **type=[type]**
Hämtar documenter av angiven typ (en eller flera). Exempel: `type=arkiv,tryckt`
- **soken_id=[id]**
Hämtar documenter upptäckt i angiven socken (en eller flera). Exempel: (sägner från Göteborgs stad och Partille) `socken_id=202,243`
- **socken=[socken namn]**
Hämtar documenter upptäckt i angiven socken, men här letar vi efter namn (etter eller flera, måste vara helt namn med ' sn' i slutet). Exempel: `socken=Fritsla sn`
- **person=[person namn]**
Hämtar documenter var upptäckare eller informant matchar angivet namn. Exempel: (alla som heter Ragnar eller Nilsson) `person=Ragnar Nilsson`
- **person_exact=[person namn]**
Hämtar documenter var upptäckare eller informant matchar angivet helt namn. Exempel: (leter bara efter "Ragnar Nilsson") `person=Ragnar Nilsson`
- **person_relation=[informant,collector]**
Säger till om `person` eller `person_exact` letar efter upptäckare eller informantar. Exempel: `person_relation=informant`

### esQuery
`def esQuery(request, query)`

Tar inn request object från Django och Elasticsearch dickionary och leverar respons från Elasticserach.

Exempel:
```python
def getTypes(request):
	query = {
		'query': createQuery(request),
		'size': 0,
		'aggs': {
			'data': {
				'terms': {
					'field': 'materialtype',
					'size': 10000,
					'order': {
						'_term': 'asc'
					}
				}
			}
		}
	}

	esQueryResponse = esQuery(request, query)
	return esQueryResponse
  ```
