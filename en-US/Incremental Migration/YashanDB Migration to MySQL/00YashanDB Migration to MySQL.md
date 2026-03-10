DMC supports online migration from YashanDB (MySQL mode) to MySQL database, including full data synchronization and incremental data synchronization.

It is recommended to perform full data synchronization when there are fewer business operations on the source side, to minimize the impact on the online business and to achieve a better user experience.

Please read the migration guidelines in the order listed in this manual before starting the migration task to avoid unexpected errors.

If the data you plan to migrate includes LOB type data, please be aware of the following limitations and memory requirements for the DMC server:

- For columns of type MEDIUMBLOB, LONGBLOB, MEDIUMTEXT, LONGTEXT, if the actual data exceeds 512MB to 1GB or exceeds the MySQL parameter `max_allowed_packet/2`, data synchronization will report an error.

  Reason: Since MySQL's communication protocol is a text protocol, when the data contains byte data of 92 ('\\'), 0 (0), 39 ('\'') (for example, LONGBLOB, LONGTEXT encoded according to its column character set), it will be escaped, requiring an additional escape character to be stored in the communication protocol. In cases where all need to be escaped, the communication packet maximum may become twice the length of the original data. Therefore, the data length synchronization is required not to exceed 512MB to 1GB due to the MySQL database's max_allowed_packet parameter settings that do not allow for exceeding 1GB.

- Due to MySQL driver limitations, for tables containing LOB columns, the memory requirement for DMC during the full synchronization phase must be greater than `(maximum row size × 6 × full concurrency (options.snapshotParallelism))`. For example, if the table structure is `(ID INT, COL LOB)` containing 10 rows, and the maximum row size is 800MB, the memory allocated to DMC must be no less than `800MB × 6 × 2 = 9.6GB` if the full concurrency setting for DMC is 2.

- Due to MySQL driver limitations, for tables containing LOB columns, the memory requirement for DMC during the incremental synchronization phase must be greater than `(maximum row size × 6 × MIN(number of LOB tables, incremental concurrency (sink.options.incrementParallelism)))`. For example, if the table structure is `(ID INT, COL LOB)` with 1 table, and 10 rows of data are inserted during the incremental phase, the maximum row size is 800MB, and the DMC incremental concurrency is set to 4, the memory allocated to DMC must be no less than `800MB × 6 × MIN(1,4) = 4.8GB`.