# ARCHITECTURE — k8s-shopify-affiliate-pocharlies

Despliegue de la Affiliate API de Skirmshop (Remix + app Shopify embebida): portal de afiliados y admin. Código en `pocharlies-org/skirmshop-affiliate-api` (fuera de la tanda; usa `@pocharlies/shopify-app-framework`).

## Clientes y versiones
- Portal `affiliate.skirmshop.es` + admin embebido, y redirector/tracking `go.skirmshop.es`. 2 réplicas (L4: un reinicio no debe tumbar el servicio) en el nodo de borde `sauvage`. Tienda Shopify propia: `ymimst-yh.myshopify.com`. Tronco: `main` (Application `shopify-affiliate`, path `k8s`).

## Dependencias (ambos sentidos)
- Base `pocharlies/k8s-shopify-framework-pocharlies//base?ref=deploy/prod`; imagen `harbor.lan.e-dani.com/homelab/shopify-affiliate-app`; Postgres compartido (base `affiliate`), Valkey compartido (usuario ACL `affiliate`: rate limiting y caché), RabbitMQ `shared-rabbitmq` vhost `/synapse` (email).

## Stack
Kustomize con base remota; sin Helm.

## Componentes compartidos
Base del framework (puerto 3462 por parche) y `pdb.yaml` (PodDisruptionBudget).

## Cómo se construye
`k8s/kustomization.yaml`, `k8s/cronjobs.yaml` (`gdpr-prune` 03:00, `sync-discounts` 03:15) y `k8s/pdb.yaml`.

## Tests y validaciones
`reusable-ci.yml` (kustomize/kubeconform).

## CI/CD y despliegue
`ci.yml`, `release.yml` (`reusable-manifest-release`), `pr-review.yml`. ArgoCD lee `main`.

## Decisiones y trampas
- El README del repo dice «rama `deploy/prod`»; lo medido en ArgoCD es `main`.
- Al ser la app de otra tienda, `SHOPIFY_APP_URL` y los dominios no son `skirmshop.e-dani.com/<prefijo>` como en el resto.
