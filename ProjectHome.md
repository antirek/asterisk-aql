AQL (Astconf Query Language) is a SQL-like statement, which can be used to write and read asterisk config files library for PHP in an easy way.

### DESCRIPTION ###

AQL focuses on making control of asterisk config files easily, fast and reliably. Users can write and read asterisk config files by writing normal SQL-like statements, and no longer need to consider how to write the right settings.

![http://asterisk-aql.googlecode.com/files/aql_flow.png](http://asterisk-aql.googlecode.com/files/aql_flow.png)

Suggestion:
  * More than 10000 extension users: use AQL to write and read standard config files except for extensions.
  * Less than 10000 extension users: use AQL to write and read all config files including extensions.
  * Less than 1000 extension users: use AQL to write and read all config files including extensions.


### SYNOPSIS ###
```
<?
include("aql.php");

// to query extension 8001 settings from sip.conf
$aql = new aql;
$aql->set('basedir','/etc/asterisk/');
$db = $aql->query("select * from sip.conf where section='8001'");
print_r($db);
?>

output:
Array
(
    [8888] => Array
        (
            [type] => friend
            .....
        )

)
```