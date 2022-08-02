:warning: **This is an undisclosed project, meaning there is no code in this repo.** This ReadMe gives a short overview over the project :warning:



# Medical Data Provenance Pipeline

This project provides an implentation of a pipeline for fetching article content and metadata from the [PubMed/MEDLINE](https://pubmed.ncbi.nlm.nih.gov) database and writing it to a PostgreSQl instance on the Adrastea Server.

## Introduction

### Motivation

The goal of this project was to set up a database holding all up-to-date [PubMed/MEDLINE](https://pubmed.ncbi.nlm.nih.gov) articles, running a daily import. These articles with their content and metadata can be used as a basis for trend and text analysis in the critical domain of biomedizine. Having all the abstracts and keywords for newly added articles one can infer new trends for diseases and their treatment or finding new branches for treatment research through connecting exisiting knowlege.

To run these important analysis, one needs fast and reliable access of the data with the ability to tag or retrive the data with advanced queries. The PubMed/MEDLINE API ([NCBI E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25501/)) lacks all of those features which explains the need for the Data Provenance Pipeline implemented in this project. Both the [yearly baseline](https://www.nlm.nih.gov/bsd/licensee/baseline.html) as well as the API have a insufficent internal data structure due to extreme variance in the organization of this data. Both are limited in speed due to their segmentation and won't provide you with advanced queries. Having the articles data stored in a SQL database was the solution to create a robust internal structure which allows for powerfull queries with no speed limitations. Having this database also enables trend analysis, if for example attaches a automatic analysis pipeline directly to the database. The advantages are huge to mirror the database and implement a daily import of new articles.

### Tasks

This project includes the implementation of the following tasks:

- Fetch PubMed/MEDLINE articles through the NCBI E-utilities
- Transform the article data structure to fit a relational model
- Design and setup efficent PostgresSQL database schema
- Create a pipeline to load the yearly baseline of article data into the database
- Create a pipeline to load daily new and updated article data into the database

### Tools

This project uses the following tools, progamming languages and concepts:

- **Python** is an interpreted high-level general-purpose programming language
- **PostgreSQL** is a free and open-source relational database management system (RDBMS)
- **SQLAlchemy** is an open-source SQL toolkit and object-relational mapper (ORM) for Python
- **Apache Airflow** is an open-source workflow management platform for data engineering pipelines
- **NCBI E-utilities** are the public API to the NCBI Entrez system and allow access to all Entrez databases including PubMed

## Implementation

This section will give you an overview over the main components and data sources of the pipeline.

<img width="1101" alt="Bildschirmfoto 2022-02-18 um 12 55 09" src="https://user-images.githubusercontent.com/44783000/154680106-a12f7d16-3504-4a8c-9718-405f6ba27104.png">

### Pipeline-Architecture

I chose to build the pipeline following the general [ETL-Arcitecture](https://en.wikipedia.org/wiki/Extract,_transform,_load) (Extract, Transform, Load), which seperates the pipeline into three main components. In this case, all the fetching and retrival of the article data is imlpemented in the Extractor. The Transformer implements all the change of structure and convertion of types, creating a new article data structure which can me imported into the SQL database. The main part of the Loader is the connection to the SQL database and giving a strucutre to savly load the article data to the defined relations.
This arcitecture helps to seperate the main actions of the pipeline, making the codebase easier to maintain.

### PubMed/MEDLINE

The main data source from which we extract the article data is the PubMed/MEDLINE database.
MEDLINE (Medical Literature Analysis and Retrieval System Online, or MEDLARS Online) is a bibliographic database of life sciences and biomedical information. It includes bibliographic information for articles from academic journals covering medicine, nursing, pharmacy, dentistry, veterinary medicine, and health care. MEDLINE also covers much of the literature in biology and biochemistry, as well as fields such as molecular evolution.

Compiled by the United States National Library of Medicine (NLM), MEDLINE is freely available on the Internet and searchable via PubMed and NLM's National Center for Biotechnology Information's Entrez system

Here you can get an overview over all databases and institues involved:
<img width="944" alt="Bildschirmfoto 2022-02-19 um 10 14 21" src="https://user-images.githubusercontent.com/44783000/154794661-1e5c2b68-74a6-4b46-8e2f-6bd91e0691ec.png">

#### PubMed Article

The main data object retrived by the PubMed database is an article, which is a published scientific paper. The article is accasible in an xml format provided by PubMed. This structure is highly unpredictable since it can change the field types based on its content. For example do all list change to singelton if there is only one element to retrieve. So after accounting for this variance and filtering the relevant data an article would look something like this:

```
{
        **undisclosed-structure**
    }
```

Chemicals, MeshTerms and PublicationType are all MeshTerms under the hood and have unique MESHUIs.

#### NCBI E-utilities Entrez (PubMed API)

The Entrez Global Query Cross-Database Search System is a federated search engine, or web portal that allows users to search many discrete health sciences databases at the National Center for Biotechnology Information (NCBI) website. The NCBI is a part of the National Library of Medicine (NLM), which is itself a department of the National Institutes of Health (NIH).

The [E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25501/) are the public API to the NCBI Entrez system and allow access to all Entrez databases. We focused on two endpoints of this interface:

- ESearch (text and date searches)

  - `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi`

  - Responds to a text or date query with the list of matching UIDs in a given database (for later use in EFetch).

- EFetch (data record downloads)

  - `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi`

  - Responds to a list of UIDs in a given database with the corresponding data records in a specified format.

### PostgresSQL

*undisclosed implementation details*

#### Database Schema

#### SQL Alchemy

### Airflow

## Set-Up

*undisclosed implementation details*

## Outlook

This section provides ideas and thoughts for further development

#### Retrival of full text

Until now PubMed only provides you with the abstract if present. But in some cases the full text is also accessible free of charge. There is already an issue providing an example on how to retrive full data. This could be implemented for all articles to enable more advanced text analysis

#### Statistics for the database

This pipeline fetches all available data points. Statistics on which data was actually provided would be usefull to understand the database in more depth. How many articles do have an abstract? How many articles do have different languges? What is the average abstract length?

#### Adapter to retrieve data

It would be great to have a Python Interface to query the database of articles. This also could be build with SQLAlchemy to retrive the articles as a `PubmedArticle` instance as mentioned above.

#### Elastic Search

We thought of inserting the JSON like article objects as JSON-Blobs in Elastic Search as a secondary database. This would be great to enable better search on the text fields like the abstract.

