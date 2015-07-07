## Overview

This package contains a number of individual XBRL filings which each represent a pass case and a fail case for each of the rules published by the Data Quality Committee. A pass case defines an XBRL instance document that is related to a given rule that will pass the rule without error. A fail case is expected to fail a given rule and return an error to the user. Every rule refers to at least a single instance that will return both a pass case and a fail case.  A user can take a given test case XBRL file and run it in a rules-aware XBRL processor and compare the result against the expected result.

## Included in the Archive

Included in the archive are the following:

### Excel Spreadsheet

An excel spreadsheet that shows the rules and the related test cases.  The excel spreadsheet documents if a pass or fail case is expected. The spreadsheet has the following columns:

1.  Rule_Element_ID:  This is a unique identifier of the element(s) that are tested within a rule
2.  Rule_ID:  The is the DQC rule number and is a unique identifier of the code that is used to process a rule.  For example the test of non negative values will only have 1 rule-id (i.e. DQC_0015) but will have multiple Rule_Element_ID’s
3.  Rule Description: This provides a description of what the rule is testing
4.  Primary Element: The primary element is provided to give the reader an idea of the element that is being tested.  If more than one element is tested in a rule, such as comparing two values only one of the elements is entered into this field.
5.  TestCase: This field is the test case identifier.  There may be multiple test cases associated with a given rule and there may also be multiple rules associated with a given test case. The name of this column corresponds to the folder containing the test case.
6.  t=error: If a testcase defines a pass condition this will have a value of false.  If the test case returns an error condition this field will have a value of true.
7.  Test_Case_Description: This describes what the test case is testing for.

In addition to the excel spreadsheet, there are a number of directories that contain the individual test cases.  To open a test case the user should open the xml instance file in the folder that is not a linkbase. A listing of the entry points is in included in the excel spreadsheet.  To open these files the user will need to use a standard XBRL processor.

### Variations File

The variation file lists the individual test cases associated with a rule.  The zip file contains a separate variation file for every rule.  Within this folder (DQC_XXXX)  is the variation file and the associated test cases for each element combination of the rule.  There is at least one pass case and one fail case associated with each rule.  The variation file documents the entry point for each of those cases.  The variation file also includes information about the expected results. The variation file includes the following information.

1.  Rule Number (i.e DQC_0036.1)
2.  Description of the Rule
3.  Rule Message. This is a message template which shows the format for any error messages returned to the user and the text to return.
4.  Name of the test case
5.  Description of the test case
6.  Location of the test case for both the schema and the instance.  These are contained in the <data> element. Each test case represents a single variation corresponding to the single instance, which is identified with the attribute readMeFirst=”true” in the <instance> element.
7.  Results for the test case.  This contains the following:
    1.  error severity
    2.  count of errors
    3.  element impacted
    4.  value of the fact impacted (If applicable)
    5.  period of the fact (start date and end date)
    6.  associated dimensions
    7.  error message

### Index File

Included in the directory is an index file.  This file lists the location of the variation file.  The variation file can then be read to determine the test cases to run with an associated rule.

### **Test Cases**

Each test case is contained in a directory called CASE_xxx.  Each file contains  a set of XBRL files that represent an individual test case.  These test cases are XBRL valid but are not EFM valid.  If running a test case it is recommended to turn off EFM validation.

## **Running the Test Cases against Arelle**

To run the test cases using Arelle:

*   Copy the zip file onto a machine that has arelle installed.
*   Unzip the files into the directory {PATH TO TESTCASE}/DQC_Testcases
*   Create the following bash file “runDQCTests.sh” using the UNIX editor of your choice.

>>> \#!/bin/bash

>>> TESTCASESROOT={PATH TO TESTCASE}/DQC_Testcases" OUTPUTLOGFILE={PATH TO WHERE OUTPUT SHOULD GO}/DQC-log.txt OUTPUTERRFILE={PATH TO WHERE OUTPUT SHOULD GO}/DQC-err.txt OUTPUTCSVFILE={PATH TO WHERE OUTPUT SHOULD GO}/DQC-report.csv TESTCASESINDEXFILE="$TESTCASESROOT/index.xml" ARELLEDIR={PATH TO WHERE ARELLE IS}

>>> rm $OUTPUTLOGFILE $OUTPUTERRFILE

>>> PYTHONPATH=$ARELLEDIR^M

>>> python3.4 -m arelle.CntlrCmdLine --file "$TESTCASESINDEXFILE" --validate --plugins "validate/DQC" --csvTestReport "$OUTPUTCSVFILE"  --logFile "$OUTPUTLOGFILE" 2>  "$OUTPUTERRFILE"

*   Run the bash file nohup scripts/runDQCTests.sh > log/nohup.out &

Note that this will take a long time to run, so leave at least 4 hours to get the results. Review the DQC-report.csv file to see the results of the test cases against the expected result.
