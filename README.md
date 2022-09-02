# Zoho-CRM-Trigger-Workflow-Assignment-Rule-Deluge
How to trigger workflow rules, assignment rules or blueprint when Zoho CRM records are created, updated, or deleted via Deluge.

## Core Idea
When a Zoho CRM record is created / updated / deleted via Deluge, it does not trigger any associated workflow rule / assignment rule / blueprint that you have set up in CRM if you do not tell it to do so. This is in a way, more of a feature than a limitation as it offers developers more flexibility. This article demonstrates the different trigger parameters needed to perform these actions.

## Tutorial

### Create / Update Records
For record creation and upadate, if you're using [Deluge task](https://www.zoho.com/deluge/help/crm/create-record.html), all you need to do is to insert a `{"trigger":{"workflow"}}` parameter after the create record / update map.

```javascript
// Create Record
mp = {"Name":"Joe","Phone":"+1 678 XXX XXXX","Email":"joe@theworkflowacademy.com"};
response = zoho.crm.createRecord("Leads",mp,{"trigger":{"workflow"}});

// Update Record
mp = {"Email":"joe@theworkflowacademy.com"};
response = zoho.crm.updateRecord("Leads",mp,{"trigger":{"workflow"}});
```
*Note: Change "Leads" and "mp" to the respective Module Name and map variable*

### Assignment Rules

To trigger assignment rules on record creation, first, you need to get the assignment rule ID (you can get it from the URL of the assignment rule), and put it in a map with "lar_id" as the key, and the assignment rule ID as the value. Then, insert it as a parameter at the end of your Deluge task.

* If you wish to trigger the assignment rule alone:
```javascript
response = zoho.crm.createRecord("Leads", mp, {"trigger":{"lar_id":"4409363000012741244"}});
```
* If you wish to trigger both workflow and assignment rule, add it inside the trigger workflow map, after the trigger workflow parameter:
```javascript
response = zoho.crm.createRecord("Leads", mp, {"trigger":{"workflow","lar_id":"xxxxxxxxxxxxxxxxxxx"});
```
*Note: Replace "xxxxxxxxxxxxxxxxxxx" with the respective assignment rule ID.*

### Blueprint

When creating or updating a record, if you want the record to enter the blueprint, you'll need to specify that under the trigger as well. 

* If you wish to trigger blueprint alone:
```javascript
response = zoho.crm.createRecord("Leads", mp, {"trigger":{"blueprint"}});
```

* If you wish to trigger workflow rules, assignment rule and blueprint:
```javascript
response = zoho.crm.createRecord("Leads", mp, {"trigger":{"workflow","blueprint","lar_id":"xxxxxxxxxxxxxxxxxxx"});
```

### Delete Records

When deleting a record via the [delete record API call](https://www.zoho.com/crm/developer/docs/api/v2/delete-records.html), you need to add the `wf_trigger=true` parameter at the end of the URL.

```javascript
response = invokeurl
[
	url: "https://www.zohoapis.com/crm/v2/Leads?ids=leadid&wf_trigger=true"
	type: DELETE
	connection:"crm_oauth_connection"
];
info response;
```
*Note: Change "Leads" and "leadid" to the respective Module Name and Record ID variable*
