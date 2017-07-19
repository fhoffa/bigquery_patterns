# BigQuery patterns

A growing collection of BigQuery patterns


### Select the latest row for each type

https://stackoverflow.com/a/45112050/132438

Traditionally we've done

    #standardSQL
    SELECT *
    FROM (
      SELECT repo.name, type, actor.id as actor, payload, created_at
        , ROW_NUMBER() OVER(PARTITION BY actor.id ORDER BY created_at DESC) rn
      FROM `githubarchive.month.201706` 
    )
    WHERE rn=1
    ORDER BY created_at
    LIMIT 100

But this sometimes fails with a "Error: Resources exceeded during query execution."

Working alternative which drops unnecessary rows instead of sorting them all:

    #standardSQL
    SELECT event.* FROM (
      SELECT ARRAY_AGG(
        t ORDER BY t.created_at DESC LIMIT 1
      )[OFFSET(0)]  event
      FROM `githubarchive.month.201706` t 
      GROUP BY actor.id
    )
    ORDER BY created_at
    LIMIT 100
    
### Costs

"The BigQuery usage logs also provide us with similar auditability and tracking. We periodically inspect the most expensive 10-20 queries in the past 30 days. These are usually due to un-optimized queries where users neglect to specify a PARTITION. Usually, the fix is as easy as adding a _PARTITIONTIME predicate to a query against a partitioned table."

https://twitter.com/felipehoffa/status/887515878262054913

### Changing schemas

"You'll get the best results in BigQuery when you can put your data in well defined columns, but you will also get great results if you just store JSON objects stored as strings."

https://stackoverflow.com/a/45043616/132438

### Partitioning tables

"BigQuery - 6 Years of Order Migration, Table / Query Design"

https://stackoverflow.com/a/45131570/132438

### Sending data to Google Cloud

![Transfer speeds](http://i.imgur.com/rqYokhf.png)

Petabyte appliance

- https://cloudplatform.googleblog.com/2017/07/introducing-Transfer-Appliance-Sneakernet-for-the-cloud-era.html

S3 transfer

- https://cloud.google.com/storage/transfer/

### Resources

- https://reddit.com/r/bigquery
- https://stackoverflow.com/questions/tagged/google-bigquery
- https://twitter.com/felipehoffa

### Disclaimer

This is not an official Google product (experimental or otherwise), it is just
code that happens to be owned by Google.
