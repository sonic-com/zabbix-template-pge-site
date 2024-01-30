# PG&E Power Status at a Site

## Overview

Zabbix Template to monitor PGE power status at a site.

Note: PGE api is undocumented and unsupported, as best I can tell. This is
worked out from what the website uses.

In other words: likely to break next time PGE updates their outages site.

### Details

PG&E's API returns a big chunk of JSON.

If there's a current outage, then `has_newer_nonsync_outage` is `true`, and
`current_outage` will be populated.

If there has been a recent outage, then `power_restored_recently` is `true`
and `most_recent_outage` will be populated.

If there are no current or recent outages, then those will be `false`, and
both `current_outage` and `most_recent_outage` will be `{}`.

### Setup

1. Visit https://pgealerts.alerts.pge.com/
2. Search for the address in question
3. Use browser tools to view what JSON is fetched.
4. Look for activity that grabs https://ewapi.cloudapi.pge.com/single-address-outages
5. Get the `prem_id` from either the URL, or from the returned JSON.
6. Create a new Host for that address in Zabbix, assign it this template,
   and set the `{$PGE.POWEROUTAGE.PREMID}` macro to the prem_id

## Macros used

## Macros used

|Name|Description|Default|Type|
|----|-----------|-------|----|
|{$PGE.POWEROUTAGE.PREMID}|prem_id from pge outages site|`8386513241`|Text macro|

## Template links

There are no template links in this template.

## Discovery rules

There are no discovery rules in this template.

## Items collected
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|PGE Outage Raw JSON|Raw JSON of site's power status from PGE|text|HTTP agent|pge.outage.raw|
|PGE Power Outage Current Boolean|true/false|Dependent Item|pge.outage.has_newer_nonsync_outage|
|PGE Power Outage Current Cause| |Dependent Item|PGE Power Outage Current Cause|
|PGE Power Outage Current Crew Current Status| |Dependent Item|pge.outage.current_outage.crew_current_status|
|PGE Power Outage Current Details|json dict|Dependent Item|pge.outage.current_outage.raw|
|PGE Power Outage Current Estimated Customers|integer |Dependent Item|pge.outage.current_outage.est_customers|
|PGE Power Outage Current Start|date+time |Dependent Item|	pge.outage.current_outage.outage_start|
|PGE Power Outage Power Restored At|date+time |Dependent Item|pge.outage.power_restored_at|
|PGE Power Outage Recent Cause| |Dependent Item|pge.outage.most_recent_outage.cause|
|PGE Power Outage Recent Crew Current Status| |Dependent Item|pge.outage.most_recent_outage.crew_current_status|
|PGE Power Outage Recent Details|json |Dependent Item|pge.outage.most_recent_outage.raw|
|PGE Power Outage Recent Estimated Customers|integer |Dependent Item|pge.outage.most_recent_outage.est_customers|
|PGE Power Outage Recent Start|date+time |Dependent Item|pge.outage.most_recent_outage.outage_start|
|PGE Power Restored Recently Boolean|true+false |Dependent Item|pge.outage.power_restored_recently|


## Triggers

|Name|Description|Expression|Priority|
|----|-----------|----------|--------|
|PGE Power Out Currently|Depends on Currently Details|`last(/PGE Power Outages at Site via HTTP REST API/pge.outage.has_newer_nonsync_outage,#1)="true"`|High|
|PGE Power Out Current Details||`last(/PGE Power Outages at Site via HTTP REST API/pge.outage.current_outage.raw,#1)<>"{}"`|High|
|PGE Power Restored Recently|Depends on Recently Details|`last(/PGE Power Outages at Site via HTTP REST API/pge.outage.power_restored_recently,#1)="true"`|Warning|
|PGE Power Out Recently Details||`last(/PGE Power Outages at Site via HTTP REST API/pge.outage.most_recent_outage.raw,#1)<>"{}"`|Warning|
