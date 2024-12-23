# Utilizar secretos dinámicos desde una aplicación web

Crear la base de datos para la aplicación:

```shell
mariadb -u root -p12345Abcde
```

```text
CREATE DATABASE blog;
```

Crear una política de control de acceso:

```shell
cat > egibide-database-policy.hcl <<EOF
path "database/egibide/*" {
    capabilities = ["list", "create", "update", "read", "delete"]
}
EOF

vault policy write egibide-database egibide-database-policy.hcl
```

Aplicar la política al usuario:

```shell
vault write auth/userpass/users/egibide/policies policies=egibide-database
```

Obtener el token del usuario:

```shell
unset VAULT_TOKEN
vault login -method=userpass username=egibide password=12345Abcde
vault token lookup
```

Arrancar la aplicación con el token.
