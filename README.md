
# IBIS (Intelligent Big data Integration System)

## Introduction

IBIS is a powerful workflow creation-engine that simplifies the process of ingesting RDBMS data into Hadoop environments. It abstracts the complex Hadoop internals, making data integration more accessible and efficient.

![Ibis Logo](/docs/ibis_wo_logo.png)

## Purpose

IBIS addresses the challenges of creating XMLs for data integration processes, which can be time-consuming and error-prone. It automates various steps, including:

- Converting Avro to Parquet
- Validating datasets
- Writing to checks & balances tables for auditing
- Making views accessible to users
- Creating backups of previous load data

With IBIS, users only need to provide a single configuration file (the "Request file") to manage these complex processes.

## Key Features

1. **Automated Workflow Generation**: IBIS automatically creates XML workflows, eliminating the need for manual XML creation.
2. **Split By Functionality**: Provides automated split by for Teradata, SQL Server, and DB2.
3. **Non-ingestion Workflow Generation**: Creates Oozie workflows from Hive and shell scripts.
4. **Schedule-based Grouping**: Groups workflows into subworkflows based on schedules for efficient automation.
5. **Parquet Storage**: Utilizes Parquet for efficient storage and fast queries.
6. **Lambda Architecture**: Stores data in the base layer as immutable data.
7. **Data Export**: Allows data export to various RDBMS systems (Oracle, DB2, SQL Server, MySQL, Teradata).
8. **Incremental Workflows**: Supports automated incremental data ingestion based on specified columns.
9. **Data Validation**: Performs row counts, DDL matching, and data sampling, storing results in a QA log table.
10. **Data Type Mapping**: Matches external data types to appropriate Hadoop data types.
11. **Isolated Environments**: Provides separate team spaces for source tables, allowing independent workflow generation and scheduling.

## How It Works

1. Create a request file with table information.
2. IBIS manages the workflow creation information.
3. IBIS generates Oozie workflows following current ingestion processes and standards.
4. IBIS creates files to simplify job scheduling through automation.
5. Execute the workflow to populate the Data Lake with RDBMS source data.

## Architecture

![IBIS Architecture](/resources/ibis-arc.png?raw=true)

## Setup

For detailed setup instructions, please refer to the [IBIS Setup Guide](/docs/setup_ibis.md).

## Usage

IBIS provides a command-line interface for various operations. Use the `--help` command to list all available functionalities:

```
./ibis-shell --help
```

For detailed usage instructions and examples, please refer to the [IBIS Features Documentation](/docs/ibis_features.md).

## Running Unit Tests

To ensure reliability and consistency, run the unit tests using:

```
python ibis_test_suite.py
```

## Contributing

We welcome contributions to the IBIS project. Please follow these steps to contribute:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Resources

- [Source Code](https://github.com/Cigna/ibis)
- [Issue Tracker](https://github.com/Cigna/ibis/issues)

## Contributors

- Matthew Wood
- Bhaskar Teja Yerneni
- Mohammad Quraishi
- IBIS logo artwork by Sam Menza

## License

This project is licensed under the Apache 2 License. See the [LICENSE](LICENSE) file for details.

Creating XMLs is a painful process, especially when including many other steps
in the process, such as converting Avro to Parquet, validating data sets,
writing to a checks & balances table for auditing, and making views accessible
to users. The XMLs also contain code that creates a backup of the previous
load's data, in case somethign goes wrong.


IBIS takes care of all of this for you, with the user's input being only one
single configuration file, also called the "Request file".

## Detailed Functionality

IBIS provides a wide range of functionalities to simplify data integration processes:

1. **Split By**: Automated split for Teradata, SQL Server, and DB2, enabling parallel execution of ingestion without manual column selection.
2. **Auto-generating Oozie Workflows**: Creates XML workflows automatically, eliminating manual XML creation.
3. **Non-ingestion Workflows**: Generates Oozie workflows from Hive and shell scripts, automating various data lake operations.
4. **Schedule-based Grouping**: Groups workflows into subworkflows based on schedules, enabling efficient automation.
5. **Parquet Storage**: Utilizes Parquet for efficient storage and fast queries.
6. **Lambda Architecture**: Implements Lambda Architecture, storing data in the base layer as immutable data.
7. **Data Export**: Supports data export from Hive to Oracle, DB2, SQL Server, MySQL, and Teradata for report generation.
8. **Incremental Workflows**: Automates incremental data ingestion based on specified columns, using where clauses for partitioned ingestion.
9. **Data Validation**: Performs row counts, DDL matching, and data sampling, storing results in a QA log table.
10. **Data Type Mapping**: Matches external data types to appropriate Hadoop data types, handling specific cases like Oracle's NUMBER type.
11. **Isolated Environments**: Provides separate team spaces for source tables, allowing independent workflow generation and scheduling.

## Command-line Interface

IBIS provides a comprehensive command-line interface. To view all available functionalities, use:

```
./ibis-shell --help
```

For detailed information on each command, refer to the [IBIS Help Documentation](/docs/help.md).

## Architecture

![Alt text](/resources/ibis-arc.png?raw=true)

## Behind the scenes

Under the covers, IBIS manages the information required to pull in data sources
into HDFS, including usernames, passwords, JBDC connection info, and also keeps
track of Automation ids, used for scheduling jobs in Production.


IBIS also has shell that allows you to run your workflow, when it's been created.
The goal is to make everything as automated as possible, so many items
(workflows, request files) are all resolved around git.

## Using the IBIS shell
### Command line interface

#### Example file

```
[Request]
split_by:MBR_KEY
mappers:10
jdbcurl:jdbc:teradata://fake.teradata/DATABASE=fake_database
db_username:fake_username
password_file:jceks://hdfs/user/dev/fake.passwords.jceks#fake.password.alias
fetch_size:50000
source_database_name:fake_database
source_table_name:fake_tablename
views:fake_view_open
weight:medium
refresh_frequency:weekly
check_column:TRANS_TIME
db_env:int
```

| Parameter | Description  |
| :--- | :--- |
| Split-by |The column used to allow the job to be run in parallel - the best type of column is a primary key or index|
| Mappers |The number of threads that can be used to connect concurrently against the source system. Always check with the DBA on the max number that can be used!|
| JDBC URL |The JDBC URL connection string for the database. See the IBIS Confluence page on specifics for different source systems|
| DB Username |The username that connects to the database|
| Password File |The encrypted password file alias and the location of the JCEKS in HDFS - contact the Big Data team if you're unsure what this is or use the Data Lake 360 Dashboard|
| Fetch Size |The number of batches to pull in at a given time; the default is 50,000. In case of OOM errors, reduce this in 10,000 increments|
| Source Database Name |The name of the database you are connecting to|
| Source Table Name |The name of the table you want to pull in|
| Views |Where you have access to query and manipulate the dataset - if you need it in more than one place, seperate it by a pipe ```'```&#124;```'```  NOTE: this column is append only. To remove all of them, update with null and then add your views.|
| Weight |Provide the size of the table (use your best judgement) on if it's '''light''','''medium''' or '''heavy'''. This is based on the number of columns and the number of rows.This effects how the workflow is generated and the number of tables that can be run in parallel. In the bakend values are translated (100 — light, 010 — medium, 001 — heavy) |
| Refresh Frequency |Only needed for Production, the frequency that is needed. This is used for reporting in checks & balances and other dashboards. All scheduling is physically done through automation. Possible values are ```none```, ```daily```, ```weekly```, ```monthly```, and ```quarterly```|
| Check Column |If you need this table ingested incrementally, provide a check column that is used to split up the load. Note that this only works for some sources right now - check with the team first|
| DB ENV |If you need to run multiple QA cycles then specify the QA cycle name(DEV/CUSTOM-ENV/PROD) the workflows will be created with corresponding filename |


#### Submit table(s) request to generate ingestion workflow

```
ibis-shell --submit-request <path to requestfile.txt> --env prod
```

> Sample Content of requestfile.txt

            [Request]
            source_database_name:fake_database           <---- db/owner name of table (mandatory)
            source_table_name:FAKE_SVC_TABLENAME                  <---- name of table (mandatory)
            db_env:sys                                     <---- env specific (optional)
            jdbcurl:jdbc:ora..                             <---- JDBC Url (optional)
            db_username:fake_username                              <---- Database username (optional)
            password_file:/user/dev/fake.password.file    <---- Database password file (optional)
            mappers:5                                      <---- Number of sqoop mappers (optional)
            refresh_frequency:weekly                       <---- if table needs to be on a schedule (optional)
                                                                       [none, daily, weekly, biweekly, fortnightly, monthly, quarterly, yearly]
            weight:heavy                                   <---- load of table (optional)
                                                                       [small, medium, heavy]
            views:fake_view_im|fake_view_open                              <---- Views (optional)
            check_column:TRANS_TIME                        <---- Column for incremental (optional)
            automation_group:magic                                <---- used for grouping tables in Automation (optional)
            fetch_size:50000                               <---- sqoop rows fetch size (optional)
            hold:1                                         <---- workflow wont be generated (optional)
            split_by:fake_nd_tablename_NUM                               <---- Used for sqoop import (recommended)
            source_schema_name:dbo                         <---- Sql-server specific schema name (optional)
            sql_query:S_NO > 50 AND ID = "ABC"             <---- Sqoop import based on custom where clause (optional)

----------

#### Test Sqoop auth credentials

```
ibis-shell --auth-test --env prod --source-db fake_database --source-table fake_dim_tablename
                --user-name fake_username --password-file /user/dev/fake.password.file
                --jdbc-url jdbc:oracle:thin:@//fake.oracle:1521/fake_servicename
```

----------
#### Export request

```
ibis-shell --export-request-prod <path to requestfile.txt> --env prod
```

> Sample Content of requestfile.txt

            [Request]
            source_database_name:test_oracle_export                    <---- db/owner name of table (mandatory)
            source_table_name:test_oracle                              <---- name of table (mandatory)
            source_dir:/user/data/mdm/test_oracle/test_oracle_export   <---- source dir (optional)
            jdbcurl:jdbc:ora...                                        <---- JDBC Url (mandatory)
            target_schema:fake_schema                                  <---- Target schema (mandatory)
            target_table:test                                          <---- Target table (mandatory)
            db_username:fake_username                                  <---- Database username (mandatory)
            password_file:/user/dev/fake.password.file                 <---- Database password file (mandatory)
            staging_database:fake_staging_database					   <---- Staging database for TD (optional)
-----------
#### Run oozie job for provided table_workflow.xml. Note: just the file name without extension

```
ibis-shell --run-job table_workflow --env prod
```

----------

#### Add table(s) to IT table

```
ibis-shell --update-it-table <path to tables.txt> --env prod
```

> Sample Content of tables.txt

        [Request]
        split_by:CLIENT_STRUC_KEY
        mappers:6
        jdbcurl:jdbc:teradata://fake.teradata/DATABASE=fake_database  # DATABASE= required for teradata
        db_username:fake_username
        password_file:/user/dev/fake.password.file
        refresh_frequency:weekly                        <---- if table needs to be on a schedule (optional)
                                                                    [none, daily, weekly, biweekly, fortnightly, monthly, quarterly]
        weight:heavy                                    <---- load of table (optional)
                                                                    [small, medium, heavy]
        fetch_size:                                     <---- No value given. Just ignores
        hold:0
        automation_appl_id:null                                <---- set null to empty the column value
        views:fake_view_im|fake_view_open               <---- Pipe(|) seperated values
        automation_group:magic_table
        check_column:fake_nd_tablename_NUM              <---- Sqoop incremental column
        source_database_name:fake_database                    (mandatory)
        source_table_name:fake_client_tablename               (mandatory)

----------

#### Creating a view
```
ibis-shell --view --view-name <view_name> --db  <name> --table <name>  # Minimum
        # OPTIONAL PARAMETERS: --select {cols} , --where {statement}
        eg. ibis-shell --view --view-name myview --db  mydb --table mytable --select col1 col2 col3 --where 'Country=USA'
```

----------

#### Create a workflow based on schedule/frequency
```
ibis-shell --gen-automation-workflows <frequency>  # frequency choices['weekly', 'monthly', 'quarterly', or 'biweekly']
```

----------

##### Create workflows with subworkflows based on one or more filters
```
ibis-shell --gen-automation-workflow schedule=None database=None jdbc_source=None
        # schedule choices - none, daily, biweekly, weekly, fortnightly, monthly, quarterly
        jdbc_source choices - oracle, db2, teradata, sqlserver
```

----------

##### Save all records or that of specific source in it-table to a file
```
ibis-shell --save-it-table --source-type sqlserver
```

----------

## Running unit tests
To ensure that IBIS can be automatically deployed, has consistency and
reliability, unit tests are a must. The development team follows TDD practices.
To run unit tests, run the following:


```
python ibis_test_suite.py
```

## Contribute
- Source Code: https://github.com/Cigna/ibis
- Issue Tracker: https://github.com/Cigna/ibis/issues

## Contributors
- Matthew Wood
- Bhaskar Teja Yerneni
- Mohammad Quraishi
- IBIS logo artwork by Sam Menza

## License
The project is licensed under the Apache 2 license

```
 ▄█  ▀█████████▄   ▄█     ▄████████
███    ███    ███ ███    ███    ███
███▌   ███    ███ ███▌   ███    █▀
███▌  ▄███▄▄▄██▀  ███▌   ███
███▌ ▀▀███▀▀▀██▄  ███▌ ▀███████████
███    ███    ██▄ ███           ███
███    ███    ███ ███     ▄█    ███
█▀   ▄█████████▀  █▀    ▄████████▀

```
