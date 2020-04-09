# Italian Public Open Data Sources

[![Data and open data on forum.italia.it](https://img.shields.io/badge/Forum-Dati%20e%20open%20data-blue.svg)](https://forum.italia.it/c/dati)
[![Public opendata sources on forum.italia.it](https://img.shields.io/badge/Thread-Verso%20l%27elenco%20# Italian Public Open Data Sources

[![Data and open data on forum.italia.it](https://img.shields.io/badge/Forum-Dati%20e%20open%20data-blue.svg)](https://forum.italia.it/c/dati)
[![Public opendata sources on forum.italia.it](https://img.shields.io/badge/Thread-Verso%20l%27elenco%20completo%20dei%20portali%20open%20data%20delle%20PA-blue.svg)](https://forum.italia.it/t/verso-lelenco-completo-dei-portali-open-data-delle-pa/12038)

[![Join the #pdnd-ckan channel](https://img.shields.io/badge/Slack%20channel-%23pdnd--ckan-blue.svg?logo=slack)](https://developersitalia.slack.com/messages/CMX9ZDPK3)
[![Get invited](https://slack.developers.italia.it/badge.svg)](https://slack.developers.italia.it/)

L'obiettivo di questo repository è di raccogliere e condividere una lista aggiornata delle fonti (source) di open data in Italia, che sia la più completa possibile e in formati leggibili dagli umani e dalle macchine.

È anche il repository ufficiale di source di raccolta per  [CKAN-IT](https://github.com/italia/ckan-it) quando configurato come _open data harvester_,
ie. all'interno della [Piattaforma Digitale Nazionale Dati (PDND) - già DAF](https://pdnd.italia.it/).
CKAN-IT fornisce tutto il necessario per eseguire CKAN, insieme ad un elenco di estensioni per supportare gli open data italiani, in un set di immagini Docker
Se siete interessati a configurare un catalogo di open data in pochi minuti, consultate [italia/ckan-it](https://github.com/italia/ckan-it).

## Organizzazioni e source di raccolta

Ci sono due entità: le organizzazioni e le source di raccolta. Entrambe sono descritte da un file json che rispetti gli _schema_ forniti.
Le organizzazioni sono nella cartella `orgs/`. Le source di raccolta, nella cartella `sources/`.

Prima di importare o subito dopo aver esportato, dovreste verificare il rispetto degli _schema_ contenuti nella cartella  `schemas/`, utilizzando i due script forniti.
È necessario avere installato Python3 con il [modulo jsonschema](https://pypi.org/project/jsonschema/) sul proprio sistema.

* Organizzazioni: `bash validate_orgs.sh`
* Source: `bash validate_sources.sh`

Se volete eseguire gli script in un environment virtuale, è disponibile un Pipfile da usare con [pipenv](https://pipenv.kennethreitz.org/en/latest/).
Se avete pipenv, basta eseguire `pipenv shell`, seguito da `pipenv install` prima di eseguire gli script di verifica.

È possibile combinare le entità delle cartelle `orgs/` e `sources/` in singoli file json usando lo script `export_all.py`, che crea due file nella cartella `dist/`:

* `index.json` con una lista di organizzazioni e source per ogni organizzazione;
* `nested/index.json` con un deeper nested object (ulteriori dettagli su [#6](https://github.com/italia/public-opendata-sources/issues/6)).

Per verificare, potete usare lo script `validate_all.sh` fornito.

### Convenzioni per i nomi

Organizzazioni:

* il nome dell'organizzazione deve corrispondere al nome per esteso qualora vi fosse anche un acronimo, ad esempio MIUR -> Ministero dell'istruzione, dell'università e della ricerca, in particolare nel caso in cui il nome dovesse includere anche la categoria (in questo caso, "Ministero").
* alcune organizzazioni sono note principalmente con il loro acronimo, ad esempio INPS. In questi casi va bene utilizzare l'acronimo, ma è necessario inserire il nome per eseso nel field description, e.g. Istituto Nazionale Previdenza Sociale nel caso dell'INPS.
* Nei nomi delle organizzazioni è necessario mantenere alcuni termini che indicano la categoria, ad esempio:
  * "Comune di"
  * "Provincia di"
  * "Provincia autonoma di"
  * "Regione"
  * "Ministero di"
  * "Città metropolitana di"
  * "Università di"

Source di raccolta:

* il nome deve rispettare la seguente regola:
  * "Catalogo open data " seguito dal nome, se presente, o il nome dell'organizzazione (e l'acronimo, se presente)
  * "Catalogo federato " seguito dal nome dell'organizzazione (e l'acronimo, se presente)
  * "Geoportale " seguito dal nome dell'organizzazione (e l'acronimo, se presente)
* se la fonte non permette la raccolta (harvesting) da parte di CKAN-IT, utilizzare `#` per il campo url

### Tipi di harvester

Al momento sono supportati i seguenti tipi di harvester di dati.

* **CKAN** (`ckan`): questo tipo di harvesting permette lo scambio di metadati tra due istanze standard di CKAN senza l'utlizzo di estensioni specifiche, utilizza le [API versione 3 fornite da CKAN](https://docs.ckan.org/en/2.6/api/index.html) e può essere utilizzato quando entrambe le istanze non rispettano alcuno standard nazionale specifico.

* **CSW server (multilang)** (`multilang`): questo tipo di harvesting importa i metadati geospaziali disponibili tramite il [protocollo CSW](https://en.wikipedia.org/wiki/Catalogue_Service_for_the_Web), l'[estensione multilang](https://github.com/italia/ckanext-multilang) gestisce i campi multilingua per dataset, gruppi, tag e organizzazioni.

* **Generic DCAT RDF Harvester** (`dcat_rdf`): questo tipo di harvesting permette di raccogliere metadati descritti con lo standard RDF ([Resource Description Framework](https://en.wikipedia.org/wiki/Resource_Description_Framework)), nel rispetto delle [specifiche DCAT](https://www.w3.org/TR/2018/WD-vocab-dcat-2-20180508/). È utilizzato per fare harvesting delle source di dati che rispettano  [DCAT-AP_IT](https://docs.italia.it/italia/daf/linee-guida-cataloghi-dati-dcat-ap-it/it/stabile/dcat-ap_it.html), una estensione che abbiamo realizzato partendo dall'[harvester DCAT di CKAN](https://github.com/ckan/ckanext-dcat).

* **DCAT JSON Harvester** (`dcat_json`): questo metodo di harvesting è usato quando i file di metadati `data.json` sono disponibili. Data.json è un API endpoint usato per descrivere cataloghi di open data, che sfrutta lo standard [U.S. Project Open Data metadata schema](https://project-open-data.cio.gov/v1.1/schema/), solitamente sistemi di gestione di dati come Socrata e DKAN offrono questo endpoint. Questo metodo di harvesting può dunque essere utilizzato in tutti i casi in cui le source dei dati sono piattaforme Socrata o DKAN. Inoltre, questo sistema di harvesting include l'harvester DCAT RDF già menzionato.

* **CKAN Harvester per DCATAPIT** (`CKAN-DCATAPIT`): questo metodo di harvesting permette lo scambio di metadati tra due istanze standard di CKAN, nel rispetto di alcune specifiche richieste da DCAT-AP_IT (e.g., mapping sui 13 data themes, e vocabolario controllato delle licenze). Questo metodo di harvesting deve essere utilizzato quando si intende fare harvesting da un catalogo che rispetta lo standard DCAT-AP_IT verso un catalogo CKAN di una data source che non rispetta lo standard DCAT-AP_IT.


* **DCAT-AP_IT CSW Harvester** (`DCAT_AP-IT CSW Harvester`): questo metodo di harvesting è utilizzato per raccogliere metadati disponibili tramite il protocollo OGC CSW. È sviluppato a partire dall'estensione spatial di CKAN, e ne eredita tutte le funzionalità. Questo harvester permette di fare harvesting dei field di dataset DCAT-AP_IT da metadati ISO. È utile quando occorre fare harvesting di open data geospaziali.

## Come importare organizzazioni e source in CKAN-IT

Installare, configurare ed eseguire CKAN dal [repository ufficiale](https://github.com/italia/ckan-it).

Qualora le immagini docker ufficiali fossero sufficienti, basta eseguirle impostando la variabile environment `CKAN_HARVEST="true"`.
Maggiori informazioni [qui](https://github.com/italia/ckan-it#ckan-it-harvesting-optional).

Altrimenti è possibile utilizzare manualmente lo script `import_all.sh` su una istanza di CKAN-IT in esecuzione.

1. Eseguire `bash import_all.sh APIKEY HOST` dove APIKEY è la chiave API del vostro utente admin ([maggiori informazioni qui](https://docs.ckan.org/en/2.6/api/index.html#authentication-and-api-keys)) e HOST è l'host CKAN (ad esempio `localhost:5000`).
2. Andare su [http://localhost:5000/organization](http://localhost:5000/organization) per verificare tutte le organizzazioni importate.
3. Andare su [http://localhost:5000/harvest](http://localhost:5000/harvest) per verificare tutte le source importate.

Poi, seguire [queste istruzioni](https://github.com/italia/ckan-it#ckan-it-harvesting-optional) per avviare il processo di harvesting di CKAN-IT.

## Come esportare organizzazioni e source

Se state eseguendo una istanza di CKAN-IT con molte source di harvesting definite (ad esempio usando l'interfaccia web), è possibile esportarle tutte con gli script `export_orgs.py` e `export_sources.py`. È necessario avere installato Python3 o usare pipenv con il Pipfile fornito.

## Come contribuire

I contributi sono i benvenuti. Sentitevi liberi di aprire issues e inviare pull request in qualsiasi momento!