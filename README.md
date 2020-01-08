# GSQ Lodgement Portal

## Purpose of this document
Provide a specification for the new GSQ Lodgement Portal that:  
* Accurately defines the business requirements
* Is a key input to software design for a software developer  

The primary input into this document is the data model defined in the [Resources Industry Report Profile](https://github.com/geological-survey-of-queensland/industry-report-profile).

## Background
Resource authority holders need to submit a range of statutory reports on their activities. Reports include all mineral exploration, geothermal exploration, greenhouse gas exploration, petroleum exploration, petroleum well, seismic survey or airborne geophysical survey, mineral development licence, petroleum lease, and petroleum pipeline licence reports and some other exploration related reporting.

When government issues a permit to a holder, the legislation requires a permit holder to submit reports to the Department when certain activities are performed under the conditions of the permit.  
* The reports contain knowledge of the geological resource.  
* Historically, industry reports have been lodged through QDEX Reports.  
* GDMP will deliver a new lodgement portal to replace QDEX Reports.  
* Most industry reports have a confidentiality period before becoming open file.  

## The Lodgement Portal

### Objectives of the Lodgement Portal
1. Provide a single portal for industry and GSQ to lodge georesources reports.
2. Provide data validation at point of data entry.
3. Provide a database store to store in-progress and submitted reports.
4. Harvest the submitted metadata, data and datasets for insertion into CKAN, S3 and the [GeoProperties Database](https://github.com/geological-survey-of-queensland/ssor-database).
5. Provide search functionality for GSQ staff to search for reports.

### Vendor deliverables
1.	An online lodgement portal to allow industry users to submit reports and upload associated geoscience data files in a structured, itemised format.  
    a.	Data must be able to be submitted through a human-computer interface  
    b.	Data must be able to be submitted through a computer-computer interface  
2.	A solution to allow internal GSQ users to manually enter and manage geoscience data and upload geoscience data files.
3.	Automated workflows to process the receipt, review, acceptance and rejection of geoscience data submitted to the data lake storage platform, including industry reports, industry data and internal data.
4.	A method of enabling the upload of large geoscience data files (up to low TB file size) to the data lake storage platform.

## Lodgement Portal conceptual data model
<p align="center">
<img src="https://github.com/geological-survey-of-queensland/gsq-lodgement-portal/blob/master/images/lodgement-portal-conceptual-design.png" width="100%"><br>
Figure 1: Lodgement Portal conceptual data model</p>

## Lodgement Portal data elements
|Element|Field name|Remarks|Source|
|---|---|---|---|
|report_id|Report ID|A unique, persistent identifer|System|
|report_alias|Report alias|An alternative identifier for the report. Digital Object Identifier will be recorded here.|System|
|report_title|Report title|A textual name|User|
|report_description|Report description|A textual description of the report|User|
|report_type|Report type|Lookup to controlled list of report types|Vocab|
|report_status|Report status|Lookup to controlled list of status|Vocab|
|report_permit|Permit|The permit number(s) covered by the report|Lookup|
|report_is_of_feature|Geoadmin feature|The features targeted in the report|Lookup|
|report_is_of_site|Site|The sites targeted in the report|Lookup|
|report_is_of_suvey|Generated by|The survey or other activity that generated the report|User|
|report_data_category|Earth science data category|The data categories featured in the report|Vocab|
|report_commodity|Commodity|The target or actual commodities featured in the report|Vocab|
|report_owner|Report owner|Party that owns the resource<br>A lookup to controlled list of organisations|Lookup|
|report_author|Authhor|Party who authored the resource|Lookup|
|report_submitter|Submitter|The logged-in user who lodged the report|System|
|report_start_time|Report period start date|The temporal coverage of the report|User|
|report_end_time|Report period end date|The temporal coverage of the report|User|
|report_lodge_time|Created|Date and time the report was lodged (date of receipt)|System|
|report_open_file_date|Issued|Date of formal issuance (open file publication)|System|
|report_access_rights|Data access rights|Controls user and system access to the resource|System|
|report_keywords|Keywords|Terms that describe the content of the report|Lookup|
|report_geometry|Geometry|Spatial coverage of the report. If no spatial data is submitted with the report and the report is for a permit(s), the spatial coverage is created based on the permit extents boundary.|WKT|
|report_details|Report details|Report-specific additional information|User|
|report_dataset|Dataset link|Links to related datasets including raw data|Hyperlink|

### Report_alias data elements (sub-table)
|Element|Field name|Remarks|Source|
|---|---|---|---|
|report_alias|Report alias|An alternative identifier for the report|User|
|report_alias_source|Alias source|The source of the alternative identifier, e.g. Digital Object Identifier|User|
|report_alias_detail|Alias detail|Details of the alias in textual form|User|

### Report_status data elements (sub-table)
|Element|Field name|Remarks|Source|
|---|---|---|---|
|report_status|Report status|Lookup to controlled list of status. In the MVP, this will default to "Submitted".|Lookup|
|status_start_date|(hidden)|The date the status was set|System|
|status_end_date|(hidden)|The date the previous status ended|System|

### Geometry data elements (sub-table)
The spatial coverage of the report. If no spatial data is submitted with the report and the report is for a permit(s), the spatial coverage is created based on the permit extents boundary. The geometry is stored in the PostGIS component of the database.

|Element|Field name|Remarks|Source|
|---|---|---|---|
|geometry_id|Geometry ID|The identifier of the geometry to allow retrieval of the geometrty.|WKT|
|geometry_format|Geometry format|The format of the geometry: a point, a line, a polygon, etc.|WKT|

### Report_details data elements (sub-table)
Enables the collection of various data, held as key:value pairs, i.e. report_detail_type:report_detail_value. e.g. tectonic:Kalkadoon

|Element|Field name|Remarks|Source|
|---|---|---|---|
|report_detail_type|Report detail type|Report-specific additional information type|Vocab|
|report_detail_value|Report detail value|Report-specific additional information in textual or numeric form|User|

### Dataset_resource data elements (sub-table)
The datasets submitted with the report, e.g. PDF files, wireline logs, CSV files, are stored as objects in S3. Each resource is described with DCAT2-compliant metadata. This table lists all of the dataset resources linked to a report.

|Element|Field name|Remarks|Source|
|---|---|---|---|
|dct:identifier|PID|Persistent identifier for the resource|System|
|dct:title|Title|A name given to the item|User|
|dct:description|Description|A free-text account of the item.|User|
|dct:type|Resource type|The nature of the resource, e.g. LAS file|Vocab|
|dct:format|Format|The file format, physical medium, or dimensions of the resource.|User|
|dct:byteSize|Byte size|The size of the resource in bytes|System|
|dct:dateSubmitted|Date submitted|Date of submission of the resource|System|

## Lodgement Portal vocabularies
The vocabularies used in this profile are:
1. [Georesources Report Type](https://vocabs.gsq.digital/vocabulary/georesource-report)
2. [Earth Science Data Category](https://vocabs.gsq.digital/vocabulary/earth-science-data-category) - the category(s) of data contained in the report
3. [Data Access Rights](https://vocabs.gsq.digital/vocabulary/data-access-rights)
4. [Commodity](https://vocabs.gsq.digital/vocabulary/gsq-commodity)
5. Report detail type.
6. Report status.

## Data Migration out of QDEX Reports

The following data is to be migrated from QDEX Reports.

|Name|Description|Count|Migrate to|
|---|---|---|---|
|QDEX - Exploration Reports|The result of mandatory reporting requirements to the government by mineral, coal and petroleum explorers in Queensland. The collection commenced with the introduction of the exploration permitting system in Queensland in the 1950's and continues to the present day with several hundred reports added annually.|97065|Lodgement Portal|
|Queensland Geological Maps|A collection of current Geological Maps published by the Geological Survey of Queensland. The collection also includes Geology Compilation Plots compiled from recent project work.|419|Open Data Portal|
|GSQ Record Series|Publications produced as part of the record series by the Geological Survey of Queensland.|1299|Open Data Portal|
|Soils and Land Resources Reports|Information on Queensland soils, acid sulfate soils, land systems, agricultural land suitability, agricultural land capability, available in land resources reports and maps and land management manuals.	|390|Open Data Portal|
|Exploration Reports|Exploration Reports specifically related to drilling and non-drilling carried out by recipients of Queensland Government exploration grants, including the Collaborative Exploration Initiative grants under the Strategic Resources Exploration Program.|119|Open Data Portal|
|Departmental Publications|Departmental Publications including Queensland Government Mining Journal (QGMJ).|2075|Open Data Portal|
|GSQ-Commissioned Industry Studies/Reports|Reports on Studies undertaken by Industry, but commissioned by the Geological Survey of Queensland.|15|Open Data Portal|
|Industry Consultative Reports|Reports created by external parties and of geological significance that are submitted to DNRM and associated with the exploration industry, but not tied to tenure or legislation|31|Open Data Portal|


## See also
* [Industry report profile](https://github.com/geological-survey-of-queensland/industry-report-profile)

## License
This code repository's content are licensed under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/), the deed of which is stored in this repository here: [LICENSE](LICENSE).


## Contacts
*System owner*:  
**Mark Gordon**  
Geological Survey of Quensland  
Department of Natural Resources, Mines and Energy  
Brisbane, QLD, Australia  
<mark.gordon@dnrme.qld.gov.au>  

*Author*:  
**David Crosswell**  
Enterprise Architect  
Cross-Lateral Enterprises   
<https://crosslateral.com.au>
