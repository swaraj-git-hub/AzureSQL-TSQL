# AzureSQL-TSQL
SQL Server Index & Statistics Maintenance Framework
Overview

This solution provides an automated SQL Server maintenance framework for managing index fragmentation and statistics updates across all user tables in a database.

The framework includes:

Automated Index Rebuild and Reorganize operations
Automated Statistics Updates
Full execution tracking using Execution IDs
Detailed logging and auditing
Error handling and troubleshooting information
Configurable maintenance settings
Historical reporting and retention management
Features
Index Maintenance

The procedure evaluates index fragmentation using sys.dm_db_index_physical_stats and performs maintenance based on Microsoft best practices:

Fragmentation %	Action0 - 5%	No Action
5 - 30%	REORGANIZE
> 30%	REBUILD

Only indexes with more than 1,000 pages are considered for maintenance.

Rebuild Optimizations

Index rebuilds are executed using:

REBUILD WITH
(
    ONLINE = ON,
    SORT_IN_TEMPDB = ON,
    MAXDOP = 2
)


Benefits:

Minimizes application downtime
Reduces TempDB contention
Limits CPU usage during maintenance
Statistics Maintenance

Statistics are updated for all user tables using one of the following modes:

Full Scan
EXEC dbo.sp_IndexAndStatsMaintenance
    @UseFullScan = 1;


Generates:

UPDATE STATISTICS [schema].[table] [stat_name] WITH FULLSCAN;

Default Sampling
EXEC dbo.sp_IndexAndStatsMaintenance
    @UseFullScan = 0;


Generates:

UPDATE STATISTICS [schema].[table] [stat_name];

Logging and Auditing

Each maintenance run generates a unique ExecutionID.

Example:

A2D09F45-2B0A-4BCA-8E8E-1E7E4F1A76D2


This enables complete traceability of all maintenance activities.

Database Objects
Stored Procedure
dbo.sp_IndexAndStatsMaintenance

Logging Tables
MaintenanceExecutionLog

Stores execution-level information:

ExecutionID
Start Time
End Time
Duration
Database Name
Server Name
Statistics Scan Mode
Execution Status

Example Status Values:

RUNNING
COMPLETED
FAILED

IndexFragmentationLog

Stores:

Fragmentation percentage
Page count
Recommended action
Executed command
Execution status
Error details

Useful for fragmentation trend analysis.

IndexMaintenanceLog

Stores detailed index maintenance information:

Schema
Table
Index Name
Action performed
Command executed
Start / End times
Success or Failure status
StatisticsUpdateLog

Stores detailed statistics update information:

Schema
Table
Statistic name
Scan type
Command executed
Execution status
Error message
MaintenanceErrorLog

Stores complete SQL Server error details:

Error Number
Error Severity
Error State
Error Procedure
Error Line Number
Full Error Message

This table provides a centralized repository for troubleshooting maintenance failures.

Execution Tracking

An Execution ID is generated for every procedure run:

DECLARE @ExecutionID UNIQUEIDENTIFIER = NEWID();


All maintenance records generated during the run are linked to the same Execution ID.

This allows administrators to:

Track a complete maintenance cycle
Correlate index and statistics activities
Identify failures within a specific execution
Produce maintenance reports
Example Executions
Daily Maintenance
EXEC dbo.sp_IndexAndStatsMaintenance
     @UseFullScan = 0,
     @MaxDop = 2,
     @OnlineRebuild = 1;


Recommended for daily execution.

Weekly Maintenance
EXEC dbo.sp_IndexAndStatsMaintenance
     @UseFullScan = 1,
     @MaxDop = 2,
     @OnlineRebuild = 1;


Recommended during maintenance windows.

Custom MAXDOP
EXEC dbo.sp_IndexAndStatsMaintenance
     @UseFullScan = 1,
     @MaxDop = 4,
     @OnlineRebuild = 1;

Retention Management

The procedure automatically cleans historical data older than 30 days from:

IndexFragmentationLog
IndexMaintenanceLog
StatisticsUpdateLog
MaintenanceErrorLog

Retention period can be adjusted by modifying:

DATEADD(DAY, -30, GETDATE())

Sample Reporting Queries
View Recent Executions
SELECT *
FROM dbo.MaintenanceExecutionLog
ORDER BY StartTime DESC;

View Failed Operations
SELECT *
FROM dbo.MaintenanceErrorLog
ORDER BY ErrorDate DESC;

View Index Rebuild Activity
SELECT *
FROM dbo.IndexMaintenanceLog
WHERE ActionTaken = 'REBUILD'
ORDER BY RunDate DESC;

View Fragmentation History
SELECT
    SchemaName,
    TableName,
    IndexName,
    Fragmentation,
    RunDate
FROM dbo.IndexFragmentationLog
ORDER BY RunDate DESC;

SQL Agent Scheduling Recommendations
Daily Job
Setting	ValueFrequency	Daily
Statistics	Default Sampling
Online Rebuild	Enabled
MAXDOP	2
Weekly Job
Setting	ValueFrequency	Weekly
Statistics	FULLSCAN
Online Rebuild	Enabled
MAXDOP	2
Benefits
Centralized maintenance process
Consistent index optimization
Improved query performance
Automated statistics management
Comprehensive auditing
Full error tracking and troubleshooting
Historical reporting capabilities
Production-ready and SQL Agent friendly
Author

SQL Server Database Operations

Version: 1.0
 Last Updated: October 2026
