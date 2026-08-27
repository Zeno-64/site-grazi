# Site Graziele Cruz — Oraculos & Magias

Site estatico de pagina unica. Sem build, sem dependencias, sem backend.
Todo CTA redireciona para o WhatsApp com mensagem pre-preenchida por servico.

## Estrutura

    public/index.html      site inteiro (CSS e JS inline)
    public/_headers        headers de seguranca
    public/assets/         favicon e imagem de compartilhamento (OG)
    wrangler.jsonc         configuracao de deploy
    .nvmrc                 versao do Node usada pelo Cloudflare

## Rodar localmente

    npx serve public

## Deploy — Cloudflare Workers (static assets)

Validar antes de publicar:

    npx wrangler deploy --dry-run

No painel: Workers & Pages > Create application > importar do Git.

    Build command:   (vazio, nao ha build)
    Deploy command:  npx wrangler deploy

Manter "Protect with Cloudflare Access" DESLIGADO, senao o site exige
login para ser visitado.

Depois disso, `git push` publica sozinho.

## Pendencias conhecidas

Registradas em `docs/decisoes-e-pendencias.md`. As duas que bloqueiam
divulgacao:

1. Links de WhatsApp usam `wa.me` para todos os dispositivos. No desktop
   isso pode abrir e fechar a aba sem abrir nada.
2. As URLs canonicas e de compartilhamento apontam para
   `grazielecruz.pages.dev`. Definir o dominio final ANTES de divulgar,
   senao o WhatsApp e o Instagram guardam a previa errada em cache.
