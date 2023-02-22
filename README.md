# CTC_API ReadMe

# What is the CTC Report API

The CTC_Report_API is a fairly new development in the HIVE Product line

#### The API allows connection to, and extraction of:

- (Current) CMS Data
- (Current) Users and Group Data
- (Current) Project Activity Data
- (Current) License Usage Data

This access replaces the need to download the Excel files on some interval
Instead, it is now possible to directly connect to the data and either efficient, or inefficiently access the resulting data

#### Common access includes:

- (Lest efficient) Direct Connection through PowerBI or Tableau
- (More efficient) Data Extraction into JSON files
  - Reviewing those files in the BI tool of choice
- (Most efficient) Extract/Transform/Load the API data and load into a database of choice
  - Update data (Without the need to fetch everything)
  - Review data in BI tool of choice connecting to 'internal' database

# Who wants to use the API

Companies who have a dedicated or experienced Data Analyst/Scientist may benefit most from the new CTC Report API
Though companies without a dedicated or experienced team, may still greatly benefit from direct access to their CTC Data

# How do you get started

A base understanding of a BI tool is recommended as reviewing the raw data can be quite tedious
BI tools often rely heavily on JSON data structure format, so an understanding in basic JSON is recommended
This sample project relies heavily on Python, but any language is capable of obtaining the data from the CTC Reporting API
Additionally, for the most efficient implementation it is recommended that SQL Server, hosted locally or on Azure be leveraged
It is possible to leverage the CTC API, targeting SQL Server Express, mySQL, PostrgreSQL, and may other database hosts

- as this project was designed to target Microsoft SQL Server, it is likely that significant code updates will be required in your fork for other databases destinations

# Using this python code

This Python code was developed in Python version 3.11.1 updated Dec 6, 2022

- Features and functions of this code may work on older versions of Python, but also, may not
- To have the best experience leveraging this code in your environment, it is recommended that your IDE leverages at least an equal version of Python

This is not a Python guide or tutorial... this section will assist in the implementation of the sample code
I certainly do not want you to have to dig for every reference and workflow
I also want to be very clear about the following:

- CTC DOES support their API
- CTC does NOT support code in this git repository
  - this sample code is provided without support
  - this code is to be used at your own risk
  - this code may be updated over time and may not function exactly as demonstrated in recorded webinars
  - this code is likely not free of bugs
  - please let the owner of this repository know if their code includes bugs, but do not reach out to CTC support or any of their partners as they will have no clue how to assist in implementing this code into your environment

## Includes leveraged

The list below will only indicate imports that need to be 'pip install'ed as they may not be included in the default install of python

- requests (for retrieving data from the CTC Api)
- logging (for generating the process logs)
- tqdm (enables progress bar display)
- json (eases parsing the json stream from the API)
- pyodbc (facilitates connecting to and updating data in the SQL server)

## Updating the settings file

There are 3 settings files, one per library

each is located at CTC_API\\`<LIBRARY>`\Settings.json
The APICore_Connection\Settings.json is a major dependency throughout all of these flows and controls connection to the official CTC API
Be sure to obtain your company reportsKey from CTC software directly and update the settings file with your key

- info@ctcsoftware.com

Also, the APICore's settings controls what data is fetched

- The default settings retrieves all of the generally relevant information and results in the most complete dataset(s)
- If you have different needs, certainly reduce the retrieved information to speed the fetch and logging of data

Finally, it is recommended that if you are leveraging the JsonCacheFiles portion that you update JSON\:Settings.json

- files:storageCachePath

## APICore

The API core implements minor throttling to be kind to the CTC API

- self throttling is recommended when tapping into any publicly available so you don't invoke throttling from the provider's side.
- If you hammer a public API , expect to be throttled at some point in the future

## Json File Pull

The Json files can be quite large and take up quite a lot of disk space if the file properties are included in the data pull
It is recommended that you begin with a limited data fetch `(controlled from the APICore_Connection\Settings)` to see how large your early file-set may be
The most effective limitation is to disable `includeTypeParameters` within `scopes:CMS:Content:optionalSwitches`
Most pulls of files will take an hour or more for an established company using HIVE CMS for a year+

## SQL Server Connection

The connection to SQL server is the most efficient data storage option long term, as the refreshing can be done using a comparison to existing data

- Only the delta needs to be retrieved, speeding refreshes for the deeper data
- Additional convenience is this code can be run in the background to refresh the database leaving the BI tool free to use throughout the refresh
- BI data and relationships can be read directly `<Direct Query>` from the SQL server, saving time in managing the BI data structure
- Python also can get all of the deep data by ID in a single pull, and then selectively store it in the database structure efficiently
  - most BI tools require multiple recursions and repeat data fetches, extending the fetch time and the load on the Official CTC API
