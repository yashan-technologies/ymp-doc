### Oracle to YashanDB Function Mapping Rules

|Order |Oracle function |YashanDB function |Support Status |
|:----|:-------------------------------------|:-------------------------------|:-----|
| 1   | ABS                                  | ABS                            | Supported   |
| 2   | ACOS                                 | ACOS                           | Supported   |
| 3   | ADD_MONTHS                           | ADD_MONTHS                     | Supported   |
| 4   | ASCII                                | ASCII                          | Supported   |
| 5   | ASIN                                 | ASIN                           | Supported   |
| 6   | ATAN                                 | ATAN                           | Supported   |
| 7   | ATAN2                                | ATAN2                          | Supported   |
| 8   | AVG                                  | AVG                            | Supported   |
| 9   | BIN_TO_NUM                           | BIN_TO_NUM                     | Supported   |
| 10  | BITAND                               | BITAND                         | Supported   |
| 11  | BITOR                                | BITOR                          | Supported   |
| 12  | BITXOR                               | BITXOR                         | Supported   |
| 13  | CAST                                 | CAST                           | Not Supported |
| 14  | CEIL                                 | CEIL                           | Supported   |
| 15  | CHR                                  | CHR                            | Supported   |
| 16  | COALESCE                             | COALESCE                       | Supported   |
| 17  | CONCAT                               | CONCAT                         | Supported   |
| 18  | COS                                  | COS                            | Supported   |
| 19  | COUNT                                | COUNT                          | Supported   |
| 20  | CURRENT_TIMESTAMP                    | CURRENT_TIMESTAMP              | Supported   |
| 21  | DECODE                               | DECODE                         | Supported   |
| 22  | DENSE_RANK                           | DENSE_RANK                     | Supported   |
| 23  | EMPTY_BLOB                           | EMPTY_BLOB                     | Supported   |
| 24  | EMPTY_CLOB                           | EMPTY_CLOB                     | Supported   |
| 25  | EXP                                  | EXP                            | Supported   |
| 26  | EXTRACT                              | EXTRACT                        | Supported   |
| 27  | FIRST_VALUE                          | FIRST_VALUE                    | Supported   |
| 28  | FLOOR                                | FLOOR                          | Supported   |
| 29  | GREATEST                             | GREATEST                       | Supported   |
| 30  | GROUPING                             | GROUPING                       | Supported   |
| 31  | GROUPING_ID                          | GROUPING_ID                    | Supported   |
| 32  | GROUP_CONCAT                         | GROUP_CONCAT                   | Not Supported |
| 33  | GROUP_ID                             | GROUP_ID                       | Supported   |
| 34  | HEXTORAW                             | HEXTORAW                       | Supported   |
| 35  | INITCAP                              | INITCAP                        | Supported   |
| 36  | INSTR                                | INSTR                          | Supported   |
| 37  | INSTRB                               | INSTRB                         | Supported   |
| 38  | JSON                                 | JSON                           | Not Supported |
| 39  | JSON_QUERY                           | JSON_QUERY                     | Not Supported |
| 40  | JSON_SERIALIZE                       | JSON_SERIALIZE                 | Not Supported |
| 41  | LAG                                  | LAG                            | Supported   |
| 42  | LAST_DAY                             | LAST_DAY                       | Supported   |
| 43  | LAST_VALUE                           | LAST_VALUE                     | Supported   |
| 44  | LEAD                                 | LEAD                           | Not Supported |
| 45  | LEAST                                | LEAST                          | Supported   |
| 46  | LENGTH                               | LENGTH                         | Supported   |
| 47  | LENGTHB                              | LENGTHB                        | Supported   |
| 48  | LENGTH2                              | LENGTH2                        | Supported   |
| 49  | LISTAGG                              | LISTAGG                        | Not Supported |
| 50  | LN                                   | LN                             | Supported   |
| 51  | LNNVL                                | LNNVL                          | Supported   |
| 52  | LOCALTIMESTAMP                       | LOCALTIMESTAMP                 | Supported   |
| 53  | LOG                                  | LOG                            | Supported   |
| 54  | LOWER                                | LOWER                          | Supported   |
| 55  | LPAD                                 | LPAD                           | Supported   |
| 56  | LTRIM                                | LTRIM                          | Supported   |
| 57  | MAX                                  | MAX                            | Supported   |
| 58  | MEDIAN                               | MEDIAN                         | Supported   |
| 59  | MIN                                  | MIN                            | Supported   |
| 60  | MOD                                  | MOD                            | Supported   |
| 61  | MONTHS_BETWEEN                       | MONTHS_BETWEEN                 | Supported   |
| 62  | NEXT_DAY                             | NEXT_DAY                       | Supported   |
| 63  | NLSSORT                              | NLSSORT                        | Supported   |
| 64  | NULLIF                               | NULLIF                         | Supported   |
| 65  | NUMTODSINTERVAL                      | NUMTODSINTERVAL                | Supported   |
| 66  | NUMTOYMINTERVAL                      | NUMTOYMINTERVAL                | Supported   |
| 67  | NVL                                  | NVL                            | Supported   |
| 68  | NVL2                                 | NVL2                           | Supported   |
| 69  | PERCENTILE_CONT                      | PERCENTILE_CONT                | Supported   |
| 70  | POWER                                | POWER                          | Supported   |
| 71  | RANK                                 | RANK                           | Supported   |
| 72  | REGEXP_COUNT                         | REGEXP_COUNT                   | Supported   |
| 73  | REGEXP_INSTR                         | REGEXP_INSTR                   | Supported   |
| 74  | REGEXP_REPLACE                       | REGEXP_REPLACE                 | Supported   |
| 75  | REGEXP_SUBSTR                        | REGEXP_SUBSTR                  | Supported   |
| 76  | REPLACE                              | REPLACE                        | Supported   |
| 77  | ROUND                                | ROUND                          | Supported   |
| 78  | ROWIDTOCHAR                          | ROWIDTOCHAR                    | Supported   |
| 79  | ROW_NUMBER                           | ROW_NUMBER                     | Supported   |
| 80  | RPAD                                 | RPAD                           | Supported   |
| 81  | RTRIM                                | RTRIM                          | Supported   |
| 82  | SCN_TO_TIMESTAMP                     | SCN_TO_TIMESTAMP               | Supported   |
| 83  | SIGN                                 | SIGN                           | Supported   |
| 84  | SIN                                  | SIN                            | Supported   |
| 85  | SINH                                 | SINH                           | Supported   |
| 86  | SOUNDEX                              | SOUNDEX                        | Supported   |
| 87  | SQRT                                 | SQRT                           | Supported   |
| 88  | STDDEV                               | STDDEV                         | Supported   |
| 89  | STDDEV_POP                           | STDDEV_POP                     | Supported   |
| 90  | STDDEV_SAMP                          | STDDEV_SAMP                    | Supported   |
| 91  | SUBSTR                               | SUBSTR                         | Supported   |
| 92  | SUBSTRB                              | SUBSTRB                        | Supported   |
| 93  | SUM                                  | SUM                            | Supported   |
| 94  | SYSDATE                              | SYSDATE                        | Supported   |
| 95  | SYSTIMESTAMP                         | SYSTIMESTAMP                   | Supported   |
| 96  | SYS_CONNECT_BY_PATH                  | SYS_CONNECT_BY_PATH            | Supported   |
| 97  | SYS_CONTEXT                          | SYS_CONTEXT                    | Supported   |
| 98  | SYS_EXTRACT_UTC                      | SYS_EXTRACT_UTC                | Supported   |
| 99  | SYS_GUID                             | SYS_GUID                       | Supported   |
| 100 | TAN                                  | TAN                            | Supported   |
| 101 | TANH                                 | TANH                           | Supported   |
| 102 | TIMESTAMP_TO_SCN                     | TIMESTAMP_TO_SCN               | Supported   |
| 103 | TO_CHAR                              | TO_CHAR                        | Supported   |
| 104 | TO_DATE                              | TO_DATE                        | Supported   |
| 105 | TO_DSINTERVAL                        | TO_DSINTERVAL                  | Supported   |
| 106 | TO_NUMBER                            | TO_NUMBER                      | Supported   |
| 107 | TO_TIMESTAMP                         | TO_TIMESTAMP                   | Supported   |
| 108 | TO_YMINTERVAL                        | TO_YMINTERVAL                  | Supported   |
| 109 | TRANSLATE                            | TRANSLATE                      | Supported   |
| 110 | TREAT                                | TREAT                          | Supported   |
| 111 | TRIM                                 | TRIM                           | Supported   |
| 112 | TRUNC                                | TRUNC                          | Supported   |
| 113 | UNISTR                               | UNISTR                         | Supported   |
| 114 | UPPER                                | UPPER                          | Supported   |
| 115 | USERENV                              | USERENV                        | Supported   |
| 116 | VARIANCE                             | VARIANCE                       | Supported   |
| 117 | VAR_POP                              | VAR_POP                        | Supported   |
| 118 | VAR_SAMP                             | VAR_SAMP                       | Supported   |
| 119 | DBMS_UTILITY.ANALYZE_SCHEMA          | DBMS_STATS.GATHER_SCHEMA_STATS | Supported   |
| 120 | DBMS_OBFUSCATION_TOOLKIT.DES3DECRYPT | DBMS_CRYPTO.DECRYPT            | Supported   |
| 121 | DBMS_OBFUSCATION_TOOLKIT.DES3ENCRYPT | DBMS_CRYPTO.ENCRYPT            | Supported   |

### Limitations

1. Other than the functions listed above, any Oracle function without a corresponding function is not supported.
2. The functions DES3DECRYPT and DES3ENCRYPT in DBMS_OBFUSCATION_TOOLKIT are only supported in YashanDB versions >= 23.1.