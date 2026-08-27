# Decisoes e pendencias

Texto sem acentos por consistencia com a convencao de commit do projeto.

## Origem

Site recebido pronto de terceiro, entregue como pasta `Codex/` mais zip.
A entrega original esta preservada no disco e fora do versionamento
(ver `.gitignore`) como fonte da verdade.

A entrega continha DUAS versoes. A adotada foi
`files-pasted-by-the-user-quero/outputs/graziele-cruz-site/`
(arquivo unico, 52 KB, CSS e JS inline). A outra
(`crie-uma-p-gina-web-pessoal/outputs/`, 3 arquivos separados, README
apontando para GitHub Pages) e rascunho anterior e foi descartada.

Os arquivos em `public/` sao copia bit a bit da versao adotada,
conferida por md5. Nenhuma linha do site foi alterada.

## Decisao: manter estatico, nao converter para Vite/React

Os outros dois sites da carteira usam Vite + React + TypeScript +
Tailwind + shadcn/ui. Este NAO foi convertido. Motivo: o site nao tem
estado, nao tem rotas, nao tem dados repetidos o suficiente para
justificar componentizacao, e nao tem build. Converter adicionaria
toolchain, node_modules, etapa de build e superficie de manutencao em
troca de nenhum ganho para o usuario final. O que a conversao daria
(componentes reutilizaveis, fonte unica de contato em `src/lib/site.ts`)
ja existe aqui de forma mais simples: o telefone esta numa unica
constante no script ao final do `index.html`.

Reavaliar apenas se aparecer: blog, catalogo que cresce, area logada,
ou mais de uma pagina.

## Auditoria feita no navegador (26/08/2026)

Servido em localhost e medido no DOM, nao a olho.

- Overflow horizontal: 375px -> scrollWidth 375 == clientWidth 375.
  1280px -> 1265 == 1265. Sem estouro.
- CTAs: 28 links com `data-service`, nenhum sem `?text=`. A mensagem
  pre-preenchida muda corretamente por servico.
- Ancoras do menu: 7 de 7 resolvem para secoes existentes.
- Alvos de toque: nav-toggle 44x44, botoes 50px de altura, float do
  WhatsApp 54x54. Todos acima de 44.
- Imagem OG: 1200x630, arte propria, verificada visualmente. Aprovada.

### Contraste WCAG AA

O tema e escuro, entao o problema recorrente dos outros dois projetos
(cor de marca clara reprovando em texto pequeno, resolvido la com as
variantes `-ink` e `-deep`) NAO ocorre aqui. Nao foi preciso criar
variante nenhuma.

Dourado #c6a15b sobre ink #0d0b0c:

    L(#c6a15b) = 0.2126(0.5646) + 0.7152(0.3565) + 0.0722(0.1046) = 0.3826
    L(#0d0b0c) = 0.2126(0.00403) + 0.7152(0.00335) + 0.0722(0.00367) = 0.0035
    (0.3826 + 0.05) / (0.0035 + 0.05) = 8.08:1

Passa AA e AAA. Vale igualmente para o texto ink sobre o botao dourado,
que usa gradiente de #c6a15b para #d8bd78.

Unica reprovacao real encontrada: `.footer-copy` (a linha de copyright),
rgb(118,108,94) a 9.92px, medido em 3.88:1 onde AA exige 4.5:1.
Falha por pouco, em texto sem importancia. Pendente.

### Nao verificado

O `.reveal` usa `animation-timeline: view()` (scroll-driven animation).
O Chrome suporta. Nao foi possivel testar o fallback nesta maquina
porque o painel do navegador nao compunha frames e o relogio de
animacao ficou congelado (`document.timeline.currentTime` avancou 0ms
em 1211ms reais) — os testes de fallback rodados deram resultado
invalido e foram descartados.

Pelo computed style observado quando `view()` cai no parse
(`animation-name: rise`, `duration: 0.7s`, `fill-mode: both`,
`timeline: auto`), o fill `both` na timeline do documento termina no
keyframe final, opacity 1. A degradacao e segura por construcao, mas
confirmar em Safari e Firefox reais antes de divulgar.

## Pendencias tecnicas

1. WhatsApp desktop. Todos os 28 links usam `wa.me`. Em desktop com o
   protocolo `whatsapp://` registrado mas sem app atras, a aba abre e
   fecha sem abrir nada — ja aconteceu em projeto anterior. Corrigir
   ramificando por dispositivo. Atencao: no ramo desktop
   (`web.whatsapp.com/send?phone=...`) a URL ja tem query string, entao
   `?text=` precisa virar `&text=`.
2. Dominio. `grazielecruz.pages.dev` esta chumbado em 6 lugares:
   canonical, og:url, og:image, og:image:secure_url, twitter:image e o
   `url` do JSON-LD. Definir o dominio final antes de divulgar.
3. `_headers` e um recurso do Cloudflare Pages. Confirmar na doc atual
   se Workers static assets honra o arquivo. Se nao honrar, os 4
   headers de seguranca nao estao ativos. Tentei deduzir pela contagem
   do `wrangler deploy --dry-run` e nao deu: o wrangler reporta sempre
   um arquivo a mais que o disco (4 no disco -> "5 files", 3 -> "4"),
   entao o offset e constante e nao diz se o `_headers` foi consumido
   como config ou tratado como asset comum. So da para resolver na doc
   oficial ou checando o header na resposta apos o primeiro deploy.
4. Contraste do `.footer-copy` (acima).

## Pendencias de conteudo — dependem da Graziele

Nada disso deve ser escrito por nos.

1. Precos publicados: R$ 67, 88, 100, 127, 167 e 197. Preco publicado e
   oferta vinculante. Confirmar cada um.
2. A secao "Condicoes de Atendimento" declara ausencia de reembolso em
   caso de desistencia. O CDC art. 49 preve direito de arrependimento
   de 7 dias em contratacao online. O texto se resguarda com "nao afasta
   direitos legalmente assegurados a consumidora", mas clausula de
   nao-reembolso em servico vendido online e terreno de disputa
   conhecido. Nao somos advogados: validar com ela.
3. Os disclaimers existentes ("nao substitui acompanhamento medico ou
   psicologico", "trabalhos espirituais nao garantem resultados
   especificos") estao bem redigidos. Manter, nao enxugar por estetica.
4. O FAQ pergunta "Preciso estar em Campinas?" mas o WhatsApp e (11),
   de Sao Paulo. Existe atendimento presencial? Onde?
5. Nao ha retrato dela no site, nem placeholder. Pela regra do projeto
   isso esta correto como esta. Perguntar se ela quer um.

Sem checkout no site — o pagamento acontece fora, pelo WhatsApp. Logo a
clausula do ToS do Cloudflare sobre processar cartao no plano free nao e
atingida.
