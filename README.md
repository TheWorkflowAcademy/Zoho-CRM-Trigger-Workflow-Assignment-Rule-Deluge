# Zoho-CRM-Trigger-Workflow-Assignment-Rule-Deluge
How to trigger workflow rules & assignment rules when Zoho CRM records are created, updated, or deleted via Deluge.

## Core Idea
When a Zoho CRM record is created / updated / deleted via Deluge, it will not trigger any associated workflow rule / assignment rule that you have set up in CRM if you do not tell it to do so. This is in a way, more of a feature than a limitation as it offers the developers more flexibility. The parameters needed to trigger workflow for creating vs editing a record is quite different. 

### Create Records
For record creation, it's rather easy and straightforward. If you're using a [Deluge task](https://www.zoho.com/deluge/help/crm/create-record.html), all you need to do is to insert a `{"trigger":{"workflow"}}` parameter after the create record map.

```javascript
mp = {"Name":"Joe","Phone":"+1 678 XXX XXXX","Email":"joe@theworkflowacademy.com"};
response = zoho.crm.createRecord("Leads",mp,{"trigger":{"workflow"}});
```
*Note: Change "Leads" and "mp" to the respective Module Name and map variable*

To trigger assignment rules on record creation, first, you need to get the assignment rule ID (you can get it from the URL of the assignment rule), then put it in a map with "lar_id" as the key, and the assignment rule ID as the value.

* If you wish to trigger the assignment rule alone:
```javascript
createRecord = zoho.crm.createRecord("Leads", mp, {"lar_id":"4409363000012741244"});
```
* If you wish to trigger both the workflow rule and assignment rule, add it inside the trigger workflow map, after the trigger workflow parameter:
```javascript
createRecord = zoho.crm.createRecord("Leads", mp, {"trigger":{"workflow"},"lar_id":"4409363000012741244"});
```
*Note: Replace "4409363000012741244" with the respective assignment rule ID.*

### Update Records
Unfortunately, the `{trigger":{"workflow"}}` parameter does not work for updates. To trigger workflows on record updates, you need to abandon Deluge task and use the following API call instead, with the exact paremeters in this specific format.

```javascript
datalist = List();
mp=Map();
mp.put("Lead_Status","Pre-Qualified"); //Here is where you insert your update map
datalist.add(mp);
triglist = List();
triglist.add("workflow");
datamap = Map();
datamap.put("data",datalist);
datamap.put("trigger",triglist);
response = invokeurl
[
  url:"https://www.zohoapis.com/crm/v2/Leads/"+leadid
  type :PUT
  parameters:datamap.toString()
  connection:"insert-crm-connection" 
];
info response;
```
*Note: Change "Leads" and "leadid" to the respective Module Name and Record ID variable*

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
