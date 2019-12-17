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

* On file uploads, there's always chance for random file inclusion vulnerability.
  Popular payload :
  ```php
  <?php
  echo file_get_contents('./flag.txt');
  ?>
  ```
* [Vulnerable PHP functions](https://stackoverflow.com/questions/3115559/exploitable-php-functions)
* PHP loose comparison: ![image](https://user-images.githubusercontent.com/42961174/70924134-0a90fa00-204f-11ea-833f-51bc2690078b.png)

* We can use result set of another `select` statement as table in a SQL query. E.g.:
  ```
   SELECT username,password FROM (SELECT * FROM users) WHERE username = xyz
  ```

* `AS` can be ommited in a sqlite query.

* `unset` function in php should not be used in an arrray as it decreases the size of array.
* `create_function` uses eval to evalute the code outside the function. So, it could be used for RCE. Sample payload:
  ```
   return -1;}phpinfo();\*
  ```
  
 * [Very cool list of facts](https://github.com/qazbnm456/awesome-security-trivia)
 
 * We could escape `preg_replace` by using similar word in between other word. E.g `UNION` as `UNUNIONION`
 
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
* Level 8
 * `exif_imagetype` just checks the file headers, and it's very easy to bypass and `getimagesize` should not be used to check image type.
