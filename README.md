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
    SELECT ARRAY_AGG(
      t ORDER BY t.created_at DESC LIMIT 1
    )[OFFSET(0)]  event
    FROM `githubarchive.month.201706` t 
    GROUP BY actor.id
    ORDER BY event.created_at
    LIMIT 100
    


### Disclaimer

This is not an official Google product (experimental or otherwise), it is just
code that happens to be owned by Google.
