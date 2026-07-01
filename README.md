# k8s-shopify-affiliate-pocharlies

Skirmshop **Affiliate API** — Remix + Shopify embedded app.

- **Public:** `affiliate.skirmshop.es` (portal de afiliados + admin embebido) y `go.skirmshop.es` (redirector de links / tracking).
- **DB:** shared postgres → `affiliate` (CNPG `postgres-shared`).
- **Redis/Valkey:** `shared-valkey` con usuario ACL `affiliate` (rate limiting + product cache, efímero).
- **Synapse email:** `shared-rabbitmq` vhost `/synapse`.
- **Tienda Shopify:** `ymimst-yh.myshopify.com` (distinta del resto de apps).

## Cluster
- **Master**: x86 ubuntu (192.168.50.142), k3s v1.32.5
- **Edge**: sauvage (WireGuard) — el pod corre aquí (`role=edge`, amd64).

## GitOps
Gestionado por ArgoCD desde [k8s-gitops-pocharlies](https://github.com/pocharlies/k8s-gitops-pocharlies)
(app `shopify-affiliate`, rama `deploy/prod`, path `k8s`).

## Imagen
`harbor.e-dani.com/homelab/shopify-affiliate-app` — construida desde el source
`shopify-affiliate-api` (migrada de docker compose en sauvage).

## Secrets
Secret Opaque `affiliate-secrets` (ns `skirmshop`) con las claves Shopify/Google/Qdrant/Synapse/pepper.
El `DATABASE_URL`, `REDIS_URL` y `SYNAPSE_AMQP_URL` se componen en el Deployment con creds de
`shared-postgres-app` y `rabbitmq-secrets`.
