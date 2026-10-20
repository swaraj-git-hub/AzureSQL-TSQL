SQL Server Index & Statistics Maintenance

Automated SQL Server maintenance framework for index optimization and statistics updates with built-in auditing, error handling, and execution tracking.

Features

Reorganizes indexes with 5%-30% fragmentation
Rebuilds indexes with >30% fragmentation
Supports ONLINE index rebuilds
Updates statistics using FULLSCAN or DEFAULT sampling
Generates a unique ExecutionID for each run
Logs all executed commands
Captures detailed error information
Maintains 30-day log retention
Objects
Stored Procedure
dbo.sp_IndexAndStatsMaintenance
Logging Tables
MaintenanceExecutionLog
IndexFragmentationLog
IndexMaintenanceLog
StatisticsUpdateLog
MaintenanceErrorLog
Example Usage
Daily Maintenance
EXEC dbo.sp_IndexAndStatsMaintenance
    @UseFullScan = 0,
    @MaxDop = 2,
    @OnlineRebuild = 1;
Weekly Maintenance
EXEC dbo.sp_IndexAndStatsMaintenance
    @UseFullScan = 1,
    @MaxDop = 2,
    @OnlineRebuild = 1;
Index Rebuild Options
REBUILD WITH
(
    ONLINE = ON,
    SORT_IN_TEMPDB = ON,
    MAXDOP = 2
)
Logging
Each execution is tracked using a unique ExecutionID, allowing correlation of:

Index maintenance activities
Statistics updates
Errors and failures
Execution duration
Commands executed
Retention
Maintenance logs older than 30 days are automatically purged during execution.

Version
v1.0
