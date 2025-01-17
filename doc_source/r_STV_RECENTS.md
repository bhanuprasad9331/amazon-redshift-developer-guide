# STV\_RECENTS<a name="r_STV_RECENTS"></a>

Use the STV\_RECENTS table to find out information about the currently active and recently run queries against a database\. 

STV\_RECENTS is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\. 

## Table columns<a name="r_STV_RECENTS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_RECENTS.html)

## Sample queries<a name="r_STV_RECENTS-sample-queries"></a>

To determine what queries are currently running against the database, type the following query: 

```
select user_name, db_name, pid, query
from stv_recents
where status = 'Running';
```

The sample output below shows a single query running on the TICKIT database: 

```
user_name | db_name |   pid   | query   
----------+---------+---------+-------------
dwuser    | tickit  |  19996  |select venuename, venueseats from 
venue where venueseats > 50000 order by venueseats desc;
```

The following example returns a list of queries \(if any\) that are running or waiting in queue to be executed: 

```
select * from stv_recents where status<>'Done';

status |    starttime        | duration |user_name|db_name| query     | pid
-------+---------------------+----------+---------+-------+-----------+------
Running| 2010-04-21 16:11... | 281566454| dwuser  |tickit | select ...| 23347
```

This query does not return results unless you are running a number of concurrent queries and some of those queries are in queue\.

The following example extends the previous example\. In this case, queries that are truly "in flight" \(running, not waiting\) are excluded from the result: 

```
select * from stv_recents where status<>'Done'
and pid not in (select pid from stv_inflight);
...
```