# Servidor de Vault

## Modo desarrollo

Arrancar los contenedores:

```shell
git clone https://github.com/egibide-ciberseguridad/vault-docker.git
```

```shell
cd vault-docker
```

```shell
cp env-example .env
```

```shell
make build
```

```shell
make start-dev
```

Acceso al UI en http://localhost:8200

Acceso a la consola:

```shell
make workspace
```

El valor del root token predeterminado esté definido en el fichero `.env`.

## Modo producción (sin TLS)

Seguir los pasos anteriores, pero al arrancarlo usar:

```shell
make start
```

Acceder al interfaz gráfico y generar la clave de desellado y el root token.

Por consola:

```shell
make workspace
```

```shell
vault status
```

```shell
vault operator init -key-shares=1 -key-threshold=1
```

```shell
vault operator unseal
```

Copiar el token de `root` al fichero `.env` y volver a iniciar el `workspace`.

## Prueba

```shell
vault secrets enable -version=2 kv
vault kv put kv/my-first-secret age=46
vault kv get kv/my-first-secret
```
