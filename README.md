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




code :


*&---------------------------------------------------------------------*
*& Report  ZPRG1_MULTIPLE_FILE_CHAT
*&
*&---------------------------------------------------------------------*
*&
*&
*&---------------------------------------------------------------------*

REPORT ZPRG1_MULTIPLE_FILE_CHAT.


*-----------------------*
* Types
*-----------------------*
TYPES: BEGIN OF ty_file_data,
         id   TYPE numc2,
         name TYPE c LENGTH 20,
       END OF ty_file_data.

*-----------------------*
* Data Declarations
*-----------------------*
DATA: lt_dir_files TYPE TABLE OF salfldir,
      ls_dir_file  TYPE salfldir,

      lt_data      TYPE TABLE OF ty_file_data,
      ls_data      TYPE ty_file_data,

      lv_source_dir TYPE salfile-longname,
      lv_target_dir TYPE c LENGTH 50 VALUE 'C:\usr\sap\EH7\SYS\src', "❗ DO NOT CHANGE
      lv_source_file TYPE string,
      lv_target_file TYPE string,
      lv_line        TYPE string,
      lv_out_line    TYPE string.

*-----------------------*
* Selection Screen
*-----------------------*
PARAMETERS: p_dir TYPE c LENGTH 100
            DEFAULT 'C:\usr\sap\EH7\DVEBMGS00\data' LOWER CASE. "❗ DO NOT CHANGE

*-----------------------*
* Initialization
*-----------------------*
lv_source_dir = p_dir.

*-----------------------*
* Read Directory
*-----------------------*
CALL FUNCTION 'RZL_READ_DIR_LOCAL'
  EXPORTING
    name     = lv_source_dir
  TABLES
    file_tbl = lt_dir_files
  EXCEPTIONS
    OTHERS   = 1.

IF sy-subrc <> 0.
  MESSAGE 'Unable to read directory' TYPE 'E'.
ENDIF.

* Keep only files starting with FILE*
DELETE lt_dir_files WHERE name NP 'FILE*'.

*-----------------------*
* Process Each File
*-----------------------*
LOOP AT lt_dir_files INTO ls_dir_file.

  CLEAR: lt_data, ls_data.

  " Build full source file path
  CONCATENATE lv_source_dir '\' ls_dir_file-name INTO lv_source_file.

  "-----------------------"
  " Open source file
  "-----------------------"
  OPEN DATASET lv_source_file FOR INPUT IN TEXT MODE ENCODING DEFAULT.
  IF sy-subrc <> 0.
    WRITE: / 'Could not open file:', lv_source_file.
    CONTINUE.
  ENDIF.

  "-----------------------"
  " Read file line by line
  "-----------------------"
  DO.
    READ DATASET lv_source_file INTO lv_line.
    IF sy-subrc <> 0.
      EXIT.
    ENDIF.

    SPLIT lv_line AT cl_abap_char_utilities=>horizontal_tab
      INTO ls_data-id ls_data-name.

    APPEND ls_data TO lt_data.
    CLEAR ls_data.
  ENDDO.

  CLOSE DATASET lv_source_file.

  IF lt_data IS INITIAL.
    WRITE: / 'No data found in file:', ls_dir_file-name.
    CONTINUE.
  ELSE.
    WRITE: / 'Data extracted from file:', ls_dir_file-name.
  ENDIF.

  "-----------------------"
  " Build target file path
  "-----------------------"
  CONCATENATE lv_target_dir '\' ls_dir_file-name INTO lv_target_file.

  "-----------------------"
  " Write to target file
  "-----------------------"
  OPEN DATASET lv_target_file FOR OUTPUT IN TEXT MODE ENCODING DEFAULT.
  IF sy-subrc <> 0.
    WRITE: / 'Could not create target file:', lv_target_file.
    CONTINUE.
  ENDIF.

  LOOP AT lt_data INTO ls_data.
    CONCATENATE ls_data-id ls_data-name INTO lv_out_line SEPARATED BY ' '.
    TRANSFER lv_out_line TO lv_target_file.
  ENDLOOP.

  CLOSE DATASET lv_target_file.

  "-----------------------"
  " Delete source file
  "-----------------------"
  DELETE DATASET lv_source_file.

  WRITE: / 'File processed and moved:', ls_dir_file-name.

ENDLOOP.
