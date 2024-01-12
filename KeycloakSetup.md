# Debian keycloak setup
2. Настройка ssh и ufw  
    Установка прав sudo
    ![](assets/KeycloakSetup/checksudo.png)
    ![](assets/KeycloakSetup/notsudo.png)
    Добавил lavakdm в файл sudoers
    ![](assets/KeycloakSetup/Pasted%20image%2020231101135215.png)
    ![](assets/KeycloakSetup/Pasted%image%20231101135337.png)
    Установка ufw
    ![](assets/KeycloakSetup/Pasted%20image%2020231101135500.png)
    Установка ssh
    ![](assets/KeycloakSetup/Pasted%20image%2020231101135610.png)
    Изменение порта ssh на 23
    ![Alt text](assets/KeycloakSetup/image-2.png)
    ![Alt text](assets/KeycloakSetup/image-1.png)
    Запрещаю подключение через root-пользователя
    ![Alt text](assets/KeycloakSetup/image-4.png)
    Разрешаю подключение только для lavakdm
    ![Alt text](assets/KeycloakSetup/image-6.png)
    Проверка
    ![Alt text](assets/KeycloakSetup/image-23.png)
    Закрытие портов, кроме 80
    ![Alt text](assets/KeycloakSetup/image-8.png)
3. Установка PostgreSQL  
   ![Alt text](assets/KeycloakSetup/image-9.png)
   ![Alt text](assets/KeycloakSetup/image-10.png)
   Создание пользователя lavakdm
   ![Alt text](assets/KeycloakSetup/image-24.png)
   Создание БД keycloak
   ![Alt text](assets/KeycloakSetup/image-25.png)
   Смена порта psql на 5433 и listen_adr
   ![Alt text](assets/KeycloakSetup/image-27.png)
   Открытие psql для доступа из вне
   ![Alt text](assets/KeycloakSetup/image-26.png)
   ![Alt text](assets/KeycloakSetup/image-28.png)
4. Установка Docker
   ![Alt text](assets/KeycloakSetup/image-18.png)
   Установка keycloak 
   ![Alt text](assets/KeycloakSetup/image-19.png)
   Создание Dockerfile
   ![Alt text](assets/KeycloakSetup/image-31.png)
   ![Alt text](assets/KeycloakSetup/image-32.png)
5. Автоматическое резервное копирование БД
   ![Alt text](assets/KeycloakSetup/image-33.png)
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
6. Создание realm   
   Создание клиента в clients
   ![Alt text](assets/KeycloakSetup/image-35.png)
   ![Alt text](assets/KeycloakSetup/image-36.png)
   ![Alt text](assets/KeycloakSetup/image-37.png)
   ![Alt text](assets/KeycloakSetup/image-38.png)
   Создание client scope. Называем его openid. Это нужно, чтобы keycloak отдавал данные пользователя по /userinfo
   ![Alt text](assets/KeycloakSetup/image-39.png)
   Создание группы. Заходим в group и создаем новую группу
   ![Alt text](assets/KeycloakSetup/image-40.png)
   Создание пользователя и присваиваем ему ранее созданную группу
   ![Alt text](assets/KeycloakSetup/image-41.png)
   В client добавляем роль 
   ![Alt text](assets/KeycloakSetup/image-43.png)
   Заходим в clients и привязываем к нему ранее созданную роль
   ![Alt text](assets/KeycloakSetup/image-44.png)
   В группе выполняем role mapping 
   Заходим в group и выполняем role mapping к ранее созданной роли
   ![Alt text](assets/KeycloakSetup/image-45.png)

7. Используя HTTP-клиенты для тестирования, такие как Insomnia и Postman, нужно протестировать отправку запросов на Keycloak
   Получение токена по паролю POST
   ![Alt text](assets/KeycloakSetup/image-46.png)
   Получение пользователей GET http://localhost:8080/realms/realm/protocol/openid-connect/userinfo
   ![Alt text](assets/KeycloakSetup/image-47.png)
   Получение токена по refresh токену POST http://localhost:8080/realms/test-realm/protocol/openid-connect/token 
   ![Alt text](assets/KeycloakSetup/image-48.png)
   Получение информации про реалм GET http://localhost:8080/realms/test-realm/.well-known/uma2-configuration
   ![Alt text](assets/KeycloakSetup/image-49.png)