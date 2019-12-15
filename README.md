# websec-fr-notes

* ## Basic SQL
  * **Union**: Combines result set of two different select queries with similar column types 
  * **Group_Concat**:  Concatenate data from multiple rows into one field
  
    __General syntax:__
    ```
    SELECT col1, col2, ..., colN
    GROUP_CONCAT ( [DISTINCT] col_name1 
    [ORDER BY clause]  [SEPARATOR str_val] ) 
    FROM table_name GROUP BY col_name2;
    ```
* ## PHP unserialize vulnerability:
   > Unserialization can result in code being loaded and executed due to object instantiation and autoloading.
   * `unserialize` can be used to inject arbitrary php objects into webserver.
   * Use php to serialize the objects by creating corresponding classes and inject the serialized output
   * See [this](https://www.notsosecure.com/remote-code-execution-via-php-unserialize/)

* ## PHP strcmp vulnerability:
  * strcmp returns zero if we compare string with an array
  * Here are some of them:
    ```
    strcmp("5", 5) => 0
    strcmp("15", 0xf) => 0
    strcmp(61529519452809720693702583126814, 61529519452809720000000000000000) => 0
    strcmp(NULL, false) => 0
    strcmp(NULL, "") => 0
    strcmp(NULL, 0) => -1
    strcmp(false, -1) => -2
    strcmp("15", NULL) => 2
    strcmp(NULL, "foo") => -3
    strcmp("foo", NULL) => 3
    strcmp("foo", false) => 3
    strcmp("foo", 0) => 1
    strcmp("foo", 5) => 1
    strcmp("foo", array()) => NULL + PHP Warning
    strcmp("foo", new stdClass) => NULL + PHP Warning
    strcmp(function(){}, "") => NULL + PHP Warning
    ```
*  On seriously malformed URLs, parse_url() may return FALSE.

**Problems**
* Level One
  * App shows first id from the resultset , so in order to get all the passwords, use `group_concat` 
  * use`union` query to inject second select statement
* Serialization is a pain!
  * Inject SQL class object
  * Corresponding SQL query to obtain password is `SELECT password AS username from users`
* Guessing is fun
  * Comparing array with string return zero
* Nobody listen to techno, nor to wrap music
  * Insert malformed url as parse_url returns false.
