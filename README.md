# mysql_sys.x-statements_with_runtimes_in_95th_percentile-

SELECT 
    `stmts`.`DIGEST_TEXT` AS `query`,
    `stmts`.`SCHEMA_NAME` AS `db`,
    IF(((`stmts`.`SUM_NO_GOOD_INDEX_USED` > 0)
            OR (`stmts`.`SUM_NO_INDEX_USED` > 0)),
        '*',
        '') AS `full_scan`,
    `stmts`.`COUNT_STAR` AS `exec_count`,
    `stmts`.`SUM_ERRORS` AS `err_count`,
    `stmts`.`SUM_WARNINGS` AS `warn_count`,
    `stmts`.`SUM_TIMER_WAIT` AS `total_latency`,
    `stmts`.`MAX_TIMER_WAIT` AS `max_latency`,
    `stmts`.`AVG_TIMER_WAIT` AS `avg_latency`,
    `stmts`.`SUM_ROWS_SENT` AS `rows_sent`,
    ROUND(IFNULL((`stmts`.`SUM_ROWS_SENT` / NULLIF(`stmts`.`COUNT_STAR`, 0)),
                    0),
            0) AS `rows_sent_avg`,
    `stmts`.`SUM_ROWS_EXAMINED` AS `rows_examined`,
    ROUND(IFNULL((`stmts`.`SUM_ROWS_EXAMINED` / NULLIF(`stmts`.`COUNT_STAR`, 0)),
                    0),
            0) AS `rows_examined_avg`,
    `stmts`.`FIRST_SEEN` AS `first_seen`,
    `stmts`.`LAST_SEEN` AS `last_seen`,
    `stmts`.`DIGEST` AS `digest`
FROM
    (`performance_schema`.`events_statements_summary_by_digest` `stmts`
    JOIN `sys`.`x$ps_digest_95th_percentile_by_avg_us` `top_percentile` ON ((ROUND((`stmts`.`AVG_TIMER_WAIT` / 1000000), 0) >= `sys`.`top_percentile`.`avg_us`)))
ORDER BY `stmts`.`AVG_TIMER_WAIT` DESC
