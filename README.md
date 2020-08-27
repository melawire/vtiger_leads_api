### How to Create Leads in Vtiger CRM Using API
This is something i wish i had when i started working with Vtiger, the official documentation is quite frustrating.  
The instruction was written for open source Vtiger CRM version 6.5.0.      

0. [ Preparation ](#prep)
1. [ Authorisation ](#auth)
2. [ Create lead ](#leadcreate)
3. [ Get lead ](#read)

<a name="prep"></a>  
### Preparation
1. **URL** for all requests is `[crm_url]/webservice.php`  
2. every user in Vtiger has **User Name** (in 'User Login & Role') and **Access Key** (in 'User Advanced Options') - we will need them both for authorisation. The User's Role needs to have enough rights to create leads.
3. `include/Webservices/Create.php` - this file receives API calls for creating leads, use it to write additional functionality and verifications. 

<a name="auth"></a>  
### Authorisation

##### 1 challenge to get token  
`[crm_url]/webservice.php?operation=getchallenge&username=[username]` 
 
request:    
```json
{
    "operation": "getchallenge",
    "username": "[username]"
}
```
response:  
```json
{
    "success": true,
    "result": {
        "token": "5e71ff1288a6c",
        "serverTime": 1584529170, 
        "expireTime": 1584529470
    }
}
```

##### 2 login to get sessionName 
POST, parameters in body.  
`[crm_url]/webservice.php?operation=login&username=[username]&accessKey=[md5(challenge_token + accesskey)]`     

request:  
```json
{
    "operation": "login",
    "username": "[username]",
    "accessKey": md5(challenge_token + [accesskey]  
}
```
response:  
```json
{
    "success": true,
    "result": {
        "SessionName": "3fc564805d9eeedcb9181",
        "userId": "19x97",
        "version": "0.22",
        "vtigerVersion": "6.5.0"
    }
}
```
this is how it looks like:  
![2](https://github.com/melawire/vtiger_leads_api/blob/master/img/2.png?raw=true)

<a name="leadcreate"></a>  
### Create lead  
POST, parameters in body. elementType is case sensitive, write ‘Leads'.  
request:  
```javascript
{
    "operation":"create",
    "format":"json",
    "sessionName":"221747465d9f2d70a76e9",
    "elementType":"Leads",
    "element": {
        "firstname":"test",
        "lastname":"test", //mandatory
        "email":"email@example.com", 
        "phone":"123456789", 
        "country":"Ukraine", 
        "website":"https://test.com/", 
        //list of mandatory fields is unique to your crm
        "assigned_user_id":"19x97" // this is parameter from authorisation response    
    }  
}
```
in response you will get all lead's fields  

this is how it looks like:  
![3](https://github.com/melawire/vtiger_leads_api/blob/master/img/3.png?raw=true)


<a name="read"></a>  
### Get lead  
GET. You can use 'query' operation for this. it functions like a standart mysql request so you can experiment with different conditions.

request:  
```json
{
    "operation": "query",
    "sessionName": "[SessionName]",
    "query": "SELECT id FROM Leads;"
}
```

more query examples:  
`SELECT * FROM Leads;`
`SELECT id FROM Leads WHERE id IN ('10x31211', '10x31212');`,  
`SELECT id FROM Leads WHERE country != ' ' LIMIT 100;`  