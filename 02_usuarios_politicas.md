# Usuarios y políticas

Inicializar el servidor.

```shell
vault operator init -key-shares=1 -key-threshold=1
vault operator unseal
```

Habilitar el almacén de secretos y crear un secreto.

```shell
vault secrets enable -path secretos -version=2 kv
vault kv put secretos/confidencial valor=1234
```

Habilitar el método de autenticación y crear el usuario.

```shell
vault auth enable userpass
vault write auth/userpass/users/egibide password=12345Abcde
```

Crear la política de acceso.

```shell
cat > egibide-secretos-policy.hcl <<EOF
path "secretos/*" {
  capabilities = ["list", "create", "update", "read", "delete"]
}
EOF

vault policy write egibide-secretos egibide-secretos-policy.hcl
```

Aplicar la política al usuario.

```shell
vault write auth/userpass/users/egibide/policies policies=egibide-secretos
```

Leer el secreto.

```shell
unset VAULT_TOKEN
vault login -method=userpass username=egibide password=12345Abcde
vault kv get secretos/confidencial
```
