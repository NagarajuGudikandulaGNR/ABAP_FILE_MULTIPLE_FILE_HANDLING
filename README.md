# ABAP_FILE_MULTIPLE_FILE_HANDLING

Title: ABAP File Processing Automation

Description:
This project demonstrates an end-to-end file handling automation program built using SAP ABAP. The program reads multiple files from the SAP application server directory, filters files based on a naming pattern, processes tab-separated data, and writes the transformed output to an archive directory. After successful processing, the source files are safely deleted.

Key Features:

Reads multiple files from an application server directory

Filters files based on filename pattern

Opens and reads files using ABAP dataset operations

Splits and processes tab-separated content

Stores data in internal tables for processing

Writes processed data to an output directory

Deletes source files after successful execution

Handles file operations using standard ABAP statements

Technologies & Concepts Used:

SAP ABAP

OPEN DATASET / READ DATASET / TRANSFER / CLOSE DATASET / DELETE DATASET

Internal Tables & Work Areas

String Operations & File Path Handling

Looping, Error Handling, and Data Processing Logic

Use Case:
This program simulates a real-world batch job used in SAP systems for inbound file processing, data transformation, and archiving.
