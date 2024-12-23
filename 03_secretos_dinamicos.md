# Secretos dinámicos

Crear el usuario para que Vault se conecte a la BD:

```shell
mariadb -u root -p12345Abcde
```

```mariadb
CREATE USER 'vault'@'%' IDENTIFIED VIA mysql_native_password USING PASSWORD('12345Abcde');
GRANT ALL PRIVILEGES ON *.* TO 'vault'@'%' WITH GRANT OPTION;
```

Probar la conexión:

```shell
mariadb -e '\q' -u vault -p12345Abcde
```

Crear el almacén de secretos para base de datos:

```shell
vault secrets enable -path=database/egibide/blog database
```

Configurar la conexión a la bd:

```shell
vault write database/egibide/blog/config/mariadb \
  plugin_name=mysql-database-plugin \
  connection_url="{{username}}:{{password}}@tcp(mariadb:3306)/" \
  allowed_roles="blog-short","blog-long" \
  username="vault" \
  password="12345Abcde"
```

Comprobar la configuración:

```shell
vault read database/egibide/blog/config/mariadb
```

Rotar la contraseña:

```shell
vault write -force database/egibide/blog/rotate-root/mariadb
```

Volver a probar:

```shell
mysql -e '\q' -u vault -p12345Abcde
```

Crear los roles:

```shell
vault write database/egibide/blog/roles/blog-long \
  db_name=mariadb \
  creation_statements="CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}'; GRANT ALL ON blog.* TO '{{name}}'@'%';" \
  default_ttl="1h" \
  max_ttl="24h"
```

```shell
vault write database/egibide/blog/roles/blog-short \
  db_name=mariadb \
  creation_statements="CREATE USER '{{name}}'@'%' IDENTIFIED BY '{{password}}'; GRANT ALL ON blog.* TO '{{name}}'@'%';" \
  default_ttl="3m" \
  max_ttl="6m"
```

Generar credenciales dinámicas con el rol de corta duración:

```shell
vault read database/egibide/blog/creds/blog-short
```

Mostrar los datos de la concesión:

```shell
vault write sys/leases/lookup lease_id=""
```

Extender la duración de la concesión:

```shell
vault write sys/leases/renew increment="300" lease_id=""
```

Revocar una concesión:

```shell
vault write sys/leases/revoke lease_id=""
```

Mostrar las concesiones:

```shell
vault list sys/leases/lookup/database/egibide/blog/creds/blog-short
```
