If you choose to use a relational database to store your data you need to keep it running smoothly and performing consistently. For this to happen there are various routine maintenance tasks that you should consider running. Typically, these tasks would be scheduled to run once a week, when you database is not being used or is not busy, though you may want to schedule them to run more or less often than this depending on how large or well-used your database is.

Here are some of the types of routine maintenance tasks you may want to consider running:

## Recover unused table space

You should recover table space left behind after data have been deleted or updated.

Different commands and terminology are used by different database types. For example, in Postgres this is done using [`VACUUM`](https://www.postgresql.org/docs/current/sql-vacuum.html). Some database types, such as Postgres, will also automatically recover space but this can happen at unpredictable times. In this instance, scheduling this maintenance during quiet times may make it less likely that space recovery will happen when the database is busy.

## Rebuild indices regularly

Indices on tables should be rebuilt periodically to recover space and prevent them from getting too fragmented.

A good option is to rebuild indices on a weekly schedule, when the system is not being used or, at the very least, when the system is not busy. Sometimes, rebuilding an index can be done in place (e.g. [`REINDEX`](https://www.postgresql.org/docs/current/sql-reindex.html) command in Postgres) or by simply dropping then recreating the index.

## Keep table and index statistics up to date

These statistics are used by the database server to work out the quickest way of running your SQL queries.

If your data vary a lot from day to day then you may want to keep your statistics up to date frequently, though if your data don't vary much over time then you have a bit more latitude. In Postgres, you can update statistics using the [`ANALYZE`](https://www.postgresql.org/docs/current/sql-analyze.html) command.

## Consider old data

If there are data in your database that you never or rarely query, and won't need to query in the future, you can consider:

1. Summarising data

    This can be a useful strategy if you have large volumes of data, and you only ever need to look at summary reports of the data. You can then summarise the detailed data into one or more tables which match the formats of the reports you need to run.

    Once you have summaries of your data you can choose either to remove or archive the original data, though archiving is usually better in case the original data should ever be needed in the future.

2. Archiving data

    Data can be expensive to store in a database. Many database providers charge based, at least partly, on the volume of data. By archiving data regularly you may be able to reduce data storage costs. Be sure that any users of your data are aware that you are archiving the data beforehand. Also, have a documented procedure for recovering the data into the database so you can query it, or, alternatively, have a process for querying the data directly from the archive.

## Upgrading infrastructure

Massive database performance improvements can usually be obtained from having maintenance tasks in place, simple queries, and supporting indices.

Consider upgrading your database infrastructure *only* after you have database maintence jobs in place, and you have optimised your queries and ensured appropriate indices are in place to support those queries.
