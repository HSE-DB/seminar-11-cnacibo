## Задание 3

1. Создайте таблицу с большим количеством данных:
    ```sql
    CREATE TABLE test_cluster AS 
    SELECT 
        generate_series(1,1000000) as id,
        CASE WHEN random() < 0.5 THEN 'A' ELSE 'B' END as category,
        md5(random()::text) as data;
    ```

2. Создайте индекс:
    ```sql
    CREATE INDEX test_cluster_cat_idx ON test_cluster(category);
    ```

3. Измерьте производительность до кластеризации:
    ```sql
    EXPLAIN ANALYZE
    SELECT * FROM test_cluster WHERE category = 'A';
    ```
    
    *План выполнения:*
    ![Решение](pics/task_3/3.png)
    
    *Объясните результат:*
    Execution Time: 128.928 ms

4. Выполните кластеризацию:
    ```sql
    CLUSTER test_cluster USING test_cluster_cat_idx;
    ```
    
    *Результат:*
    ![Решение](pics/task_3/4.png)

5. Измерьте производительность после кластеризации:
    ```sql
    EXPLAIN ANALYZE
    SELECT * FROM test_cluster WHERE category = 'A';
    ```
    
    *План выполнения:*
    ![Решение](pics/task_3/5.png)
    
    *Объясните результат:*
    Execution Time: 93.525 ms

6. Сравните производительность до и после кластеризации:
    
    *Сравнение:*
    Производительность после кластеризации увеличилась так как
время выполнения уменьшилось на 35 ms, что составляет 27%.