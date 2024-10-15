# ibis/ibis/driver Documentation

This directory contains the core driver functionality for the ibis project.

## main.py

The `main.py` module serves as the entry point for the ibis CLI. It contains the following key components:

- `main()`: The main function that parses command-line arguments and calls appropriate functionality handler methods.
- Various handler functions for different operations, such as:
  - Generating workflows
  - Exporting data
  - Managing the IT table
  - Performing authentication tests
  - Running Oozie jobs
  - Handling checks and balances

## driver.py

The `driver.py` module contains the `Driver` class, which provides methods to be accessed via the CLI. Key functionalities include:

- Submitting requests
- Generating workflows (including production, incremental, and subworkflows)
- Exporting data (to Teradata and Oracle)
- Managing the IT table
- Handling authentication
- Performing backup and restore operations
- Managing lifespans in the Check and Balances table

The `Driver` class serves as the main interface for the ibis project's functionality, handling various operations related to data ingestion, workflow management, and exports.

## __init__.py

This file is empty and serves to mark the directory as a Python package.

Note: The codebase appears to be using Python 2, which may have implications for future maintenance and compatibility.