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
3. RESOLVIDO. O `_headers` E honrado por Workers static assets. Prova:
   `curl -sI https://site-grazi.kevinbaradelli.workers.dev` devolve os
   quatro headers do arquivo — `x-content-type-options: nosniff`,
   `x-frame-options: SAMEORIGIN`, `referrer-policy:
   strict-origin-when-cross-origin` e `permissions-policy: camera=(),
   microphone=(), geolocation=()`. Nao e preciso migrar para Pages.

4. Contraste do `.footer-copy` (acima).
5. Nome do worker divergente. O worker no ar chama-se `site-grazi`
   (`site-grazi.kevinbaradelli.workers.dev` responde 200), mas o
   `wrangler.jsonc` deste repo declara `grazielecruz`
   (`grazielecruz.kevinbaradelli.workers.dev` responde 404). Um
   `wrangler deploy` a partir daqui criaria um SEGUNDO worker em vez de
   atualizar o existente. Reconciliar antes do proximo deploy.
6. Exposicao do desenvolvedor. O repositorio e publico (`private:
   false`) e todo commit carrega nome e email pessoal do desenvolvedor
   no metadado de autoria, consultavel pela API do GitHub sem
   autenticacao. Decidir entre tornar o repo privado, passar a usar o
   email `noreply` do GitHub, ou aceitar conscientemente.
7. Subdominio workers.dev embutido na conta. Enquanto o
   `workers.dev` nao for desligado explicitamente
   (`"workers_dev": false` no wrangler.jsonc — chave validada como
   aceita pelo wrangler 4.126), a URL com o nome do desenvolvedor
   continua no ar e indexavel mesmo depois de o dominio proprio
   entrar no ar. Adicionar dominio proprio NAO desliga a workers.dev.

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

## Secao Mentoria e Comunidade (adicionada 26/08/2026)

Duas abas acessiveis numa secao nova (`#mentoria`), inserida entre os
jars e "Como funciona". Fundo ink puro para nao repetir o wine da secao
anterior e para dar o respiro pedido no briefing.

Decisoes:

- Construida com as convencoes que ja existiam (`.section`, `.eyebrow`,
  `.section-title`, `.gold-rule`, `.button`, `.reveal`), sem sistema
  visual paralelo.
- Abas com ARIA real: `role=tablist/tab/tabpanel`, `aria-selected`,
  `aria-controls`, roving tabindex e teclado (setas, Home, End, com
  wrap-around). Verificado no navegador.
- Sem JavaScript a secao continua utilizavel: um `<noscript>` desfaz o
  `hidden` dos paineis e esconde a lista de abas, entao os dois
  conteudos aparecem empilhados. Evita tambem o pulo de layout que
  haveria se o painel fosse escondido so depois do load.
- Os botoes dos grupos NAO levam `data-service`. O script existente
  reescreve o href de todo `[data-service]` para `wa.me`; com o
  atributo, os links de convite seriam destruidos. Conferido: os dois
  links de grupo chegam intactos ao DOM e os 28 CTAs originais seguem
  com a mensagem pre-preenchida.
- Estrela de Davi usada como marca d'agua unica a 5,5% de opacidade,
  conforme o pedido de simbolo "extremamente sutil".
- Contraste da secao: zero reprovacoes AA nas duas abas.

### Regressao causada e corrigida

Adicionar o oitavo item no menu ("Mentoria") espremeu a barra
horizontal em 900px: o menu foi de 611px para 669px, sobraram 16px ate
a marca e os rotulos "Baralho Cigano" e "Como funciona" quebraram em
duas linhas. Medido com e sem o item para confirmar a causa.

Corrigido movendo a navegacao horizontal de 900px para 1024px (entre
900 e 1024 usa o hamburguer) e travando `white-space: nowrap` nos
rotulos. Folga apos a correcao: 117px em 1024px, 292px em 1280px,
nenhum rotulo quebrado em nenhum dos tres breakpoints.

## Pendencias de conteudo da secao nova

1. A data "Início: 10 de setembro" esta escrita no HTML. Site estatico,
   sem painel: quando a turma virar, alguem precisa editar o arquivo.
   Combinar com a Graziele como ela avisa.
2. Os links de convite de grupo do WhatsApp ficam publicos numa pagina
   indexavel. Consequencias que ela precisa saber: qualquer pessoa com
   o link entra, entao "exclusivo para mulheres" passa a depender de
   moderacao e nao do link; membros de um grupo enxergam o telefone uns
   dos outros; e link de convite exposto em pagina publica costuma ser
   coletado por bot de spam. Se isso incomodar, a alternativa e o
   botao apontar para o WhatsApp dela e o convite ser enviado no
   particular.
3. A Jornada e o unico servico do site sem valor declarado — fala em
   "valor de troca" sem numero. Confirmar se e proposital.
4. O briefing pediu que o site nao pareca religioso, mas o conteudo
   trata de Orixas, Axe e ancestralidade. Segui a direcao de design
   (tipografia e simbolo discreto, nenhuma imagem religiosa); a
   decisao editorial e dela.
