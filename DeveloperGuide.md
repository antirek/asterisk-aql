# DeveloperGuide #

## Installation ##

Coping inc/ to your script including folder.
Add one line at header of your script within aql.php, example:

```

<?
require("../inc/aql.php");

$a = new aql();

if (!$a->set('basedir','./')) {
    echo __LINE__.' '.$a->get_error();
}

$a->query(....);
.....
?>
```

## CommandLine ##

commandline interface looks like mysql monitor.

in aql source folder:
```
chmod +x aqlcli.php
./aqlcli.php <config_base_path>
```

example:
```
./aqlcli.php /etc/asterisk/

Welcome to the AQL command line interface.  Commands end pass Enter key
Your AQL Library version : 1.0 ,   AQL_Confparser Version : 0.3

type 'help' for help.

AQLcli>select * from sip.conf
```

you will see output data


## Methods ##

### $AQL->set(option,value) ###

set An options to AQL object.
  * basedir : [//path//] load config files on base directory, you must specify path before all query.
  * autocommit : [true|false] each query command auto commit, tune false to disable auto commit and manual call $aql——>commit(...) to do.
  * reloadwhensave : [true|false]
  * queryresult : [true|false]
  * comment\_flags : [#|;] comment char in config files.


### $AQL->query(expression) ###

Query AQL statements to config file and get results.

References with query language in detail please see **Query Language Interface Reference**

### $AQL->commit($filename) ###

Commit all query command to AQL conf parser engine. System will call this function automatically, unless tune false to the option 'autucommit'.

```
# system step by step to run two query commands.
$AQL->set('autocommit','false');
$AQL->query("select * from sip.conf");
$AQL->query("delete from sip.conf where section='8001'");
$AQL->commit("sip.conf");
```

### $AQL->commit\_clean($filename) ###

Means clean all commit command. Users will not use this function until tune false to the option 'autocommit'



### $AQL->get\_error() ###

Return string including the last error messages from config parser engine.

### $AQL->get\_affected\_rows() ###

Get the number of affected rows by the last AQL commands.


## Query-Language-Interface-Reference ##

### BasicSyntax ###

  * each asterisk conf file name is SQL table name
  * each section name is SQL table's field "section" and its PRIMARY KEY and its static key in any results
  * when select each dupcopy key will auto-convert to single string separated by ","
  * more than one key
  * duplication of key name in asterisk convert in AQL append with [N](N.md),example allow=g729 allow=g723 to allow allow[1](1.md), but the frist key not changed.
  * if unsection will assign sectionname '[unsection](unsection.md)', in query section = null equal section = '[unsection](unsection.md)'



### Expressions ###

The AQL Query syntax can be summarized as follows:
```

query from config files:

  SELECT * FROM <config_file>
    [WHERE <condition> [AND <condition> ...]]
    [LIMIT [<offset>,]<count>]

edit keys :

  UPDATE <config_file> SET key=value[,key2=value2]
    [WHERE <condition> [AND <condition> ...]]
    [LIMIT [<offset>,]<count>]
    
add new key :

  ALTER TABLE <config_file> ADD key=value[,key2=value2...] [AFTER(keyname)|BEFORE(keyname)]
    [WHERE <condition> [AND <condition> ...]]
    [LIMIT [<offset>,]<count>]
    
delete key :

  ALTER TABLE <config_file> DROP key,[key2...]
    [WHERE <condition> [AND <condition> ...]]
    [LIMIT [<offset>,]<count>]

add new section and datas:

  INSERT INTO <config_file> SET key=value[,key2=value2]
  
delete section :

  DELETE FROM <config_file>
    [WHERE <condition> [AND <condition> ...]]
    [LIMIT [<offset>,]<count>]

use other path(like $aql->set('basedir',path)) :

  USE <path>
  
```

As with SQL, AQL keywords are case insensitive. but table-name and field and value are case sensitive.

### Condition ###

Only key name "section =" can be supported, because "section" is unique in asterisk config files.

The optional WHERE clause filters the result set to those entities that meet one or more conditions.
Each condition compares a property of the entity with a value using a comparison operator and multiple conditions are given with the AND keyword. AQL does not have an ""OR"" operator.
```
# query results from 8001 to 8004 extensions.
where section >= 8001 and section <= 8004
# query results from unsection in any files.
where section = ''
```

### Operators ###

```
comparison digit : = != < > <= >=
match string :
like '%string'
like 'string%'
like '%string%'
```

Note: like '%string%' is very slowly.

### Limit ###

An optional LIMIT clause causes the query to stop returning results after the first count entities. The LIMIT can also include an offset to skip that many results to find the first result to return. An optional OFFSET clause can specify an offset if no LIMIT clause is present.

```
limit 10
limit 10,10
```

## Function-Based-Interface-Reference ##

Function based interface inheriting aql\_confparser.php. that advanced class, for the beginnger we suggest try to Query Language Interface.

### get\_version() ###

return the version of aql\_confparser.php

### get\_database() ###

return the full data from database variable.

Note: database variable include all of config files.

### get\_config\_array($filename) ###

return parsed conf from database.

### get\_commit\_assign($filename) ###

return commit assigned list.

### open\_config\_file($filename) ###

open config file from filename

Note: database variable include all of config files.

### open\_reload($filename) ###

try to reload and reparse config with filename.

### save\_config\_file($filename) ###

do commited change and save to config file.

### check\_database\_exists($filename) ###

checking database's filename exists, return true or false.

### free\_database() ###

set database variable null.

### assign\_append($section,$key,$value,$position=null,$match\_key=null) ###

assign append data around with section name.

$position:
  * null : append to end of section, or before space line in section.
  * before : append before keyname must set 'match\_keyname'
  * after : append after keyname must set 'match\_keyname'


### assign\_delkey($section,$key) ###

assign delkey from section in config files.

### assign\_addsection($section,$datachunk) ###

add section with special name and data chunk.

### assign\_delsection($section) ###

drop section with section name.

# AUTHOR #

Sun bing <hoowa.sun@gmail.com>.