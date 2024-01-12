# Debian keycloak setup
1. Настройка ssh и ufw  
    Установка прав sudo
    ![](assets/README/checksudo.png)
    ![](assets/README/notsudo.png)
    Добавил lavakdm в файл sudoers
    ![](assets/README/Pasted%20image%2020231101135215.png)
    ![](assets/README/Pasted%image%20231101135337.png)
    Установка ufw
    ![](assets/README/Pasted%20image%2020231101135500.png)
    Установка ssh
    ![](assets/README/Pasted%20image%2020231101135610.png)
    Изменение порта ssh на 23
    ![Alt text](assets/README/image-2.png)
    ![Alt text](assets/README/image-1.png)
    Запрещаю подключение через root-пользователя
    ![Alt text](assets/README/image-4.png)
    Разрешаю подключение только для lavakdm
    ![Alt text](assets/README/image-6.png)
    Проверка
    ![Alt text](assets/README/image-23.png)
    Закрытие портов, кроме 80
    ![Alt text](assets/README/image-8.png)
2. Установка PostgreSQL  
   ![Alt text](assets/README/image-9.png)
   ![Alt text](assets/README/image-10.png)
   Создание пользователя lavakdm
   ![Alt text](assets/README/image-24.png)
   Создание БД keycloak
   ![Alt text](assets/README/image-25.png)
   Смена порта psql на 5433 и listen_adr
   ![Alt text](assets/README/image-27.png)
   Открытие psql для доступа из вне
   ![Alt text](assets/README/image-26.png)
   ![Alt text](assets/README/image-28.png)
3. Установка Docker
   ![Alt text](assets/README/image-18.png)
   Установка keycloak 
   ![Alt text](assets/README/image-19.png)
   Создание Dockerfile
   ![Alt text](assets/README/image-31.png)
   ![Alt text](assets/README/image-32.png)
4. Автоматическое резервное копирование БД
   ![Alt text](assets/README/image-33.png)
   postgresql_dump.sh
   ```shell
   #!/bin/sh
   PATH=/etc:/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin

   PGPASSWORD=password
   export PGPASSWORD
   pathB=/backup
   dbUser=dbuser
   database=db

   find $pathB \( -name "*-1[^5].*" -o -name "*-[023]?.*" \) -ctime +61 -delete
   pg_dump -U $dbUser $database | gzip > $pathB/pgsql_$(date "+%Y-%m-%d").sql.gz

   unset PGPASSWORD
   ```
5. Создание realm   
   Создание клиента в clients
   ![Alt text](assets/README/image-35.png)
   ![Alt text](assets/README/image-36.png)
   ![Alt text](assets/README/image-37.png)
   ![Alt text](assets/README/image-38.png)
   Создание client scope. Называем его openid. Это нужно, чтобы keycloak отдавал данные пользователя по /userinfo
   ![Alt text](assets/README/image-39.png)
   Создание группы. Заходим в group и создаем новую группу
   ![Alt text](assets/README/image-40.png)
   Создание пользователя и присваиваем ему ранее созданную группу
   ![Alt text](assets/README/image-41.png)
   В client добавляем роль 
   ![Alt text](assets/README/image-43.png)
   Заходим в clients и привязываем к нему ранее созданную роль
   ![Alt text](assets/README/image-44.png)
   В группе выполняем role mapping 
   Заходим в group и выполняем role mapping к ранее созданной роли
   ![Alt text](assets/README/image-45.png)

6. Используя HTTP-клиенты для тестирования, такие как Insomnia и Postman, нужно протестировать отправку запросов на Keycloak
   Получение токена по паролю POST
   ![Alt text](assets/README/image-46.png)
   Получение пользователей GET http://localhost:8080/realms/realm/protocol/openid-connect/userinfo
   ![Alt text](assets/README/image-47.png)
   Получение токена по refresh токену POST http://localhost:8080/realms/test-realm/protocol/openid-connect/token 
   ![Alt text](assets/README/image-48.png)
   Получение информации про реалм GET http://localhost:8080/realms/test-realm/.well-known/uma2-configuration
   ![Alt text](assets/README/image-49.png)