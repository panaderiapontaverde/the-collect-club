# The Collect Club

Painel de gestão do negócio de compra e revenda de relógios de coleção (compra no Japão via 2nd Street, revenda no Brasil via OLX/Instagram).

Site publicado: https://panaderiapontaverde.github.io/the-collect-club/

Fonte de dados: aba de gestão no Google Sheets ("The Collect Club - Gestão"). O `index.html` é um snapshot manual dessa planilha — toda vez que a planilha muda, os arrays de dados no `<script>` do `index.html` precisam ser atualizados manualmente e republicados (ver processo abaixo).

## Estrutura do site

Site estático de página única (`index.html`), sem build step. Todo HTML/CSS/JS em um arquivo só. Abas: Visão Geral, Financeiro, Patrimônio, Estoque, Pesquisa de Mercado, Vendas.

## Processo de publicação (checklist)

1. **Se o estado local do `index.html` for suspeito** (sessão interrompida, arquivo mais curto que o esperado): recuperar a versão publicada mais recente do GitHub antes de editar:
   ```
   curl -sL -o index.html "https://raw.githubusercontent.com/panaderiapontaverde/the-collect-club/main/index.html"
   ```
   Isso é mais confiável do que assumir que o disco local está correto.

2. **Editar** o `index.html` localmente.

3. **Verificar localmente** antes de publicar, com um script Playwright que abre o arquivo, navega pelas abas relevantes e tira screenshot (evita publicar HTML quebrado). Usar `executablePath: '/opt/pw-browsers/chromium'`.

4. **Publicar no GitHub**: ir em `https://github.com/panaderiapontaverde/the-collect-club/upload/main`, fazer upload do `index.html` (substitui o existente) e clicar em "Commit changes".
   - Se o clique no botão não submeter (acontece com frequência), clicar nas coordenadas do pixel do botão em vez de usar o seletor.

5. **Aguardar o deploy do GitHub Pages** — checar em `https://github.com/panaderiapontaverde/the-collect-club/deployments` até aparecer "Active", ou em `/actions` para ver o progresso do "pages build and deployment". Em dias de instabilidade do GitHub, isso pode levar bem mais que o normal (já chegou a 20+ minutos).

6. **Verificar o conteúdo publicado** com um fetch anti-cache antes de considerar concluído (o `fetch()` simples às vezes serve conteúdo antigo do CDN mesmo com o deploy já "Active"):
   ```js
   const r = await fetch(url + "?x=" + Math.random(), {cache: "no-store"});
   ```

## Regras de negócio codificadas no site

- Perfil de compra (TAG Heuer: séries 2000/Aquaracer/Fórmula 1/Professional, caixa ~38mm, pulseira ~18,5cm; Seiko Prospex) e restrições (não gosta de pulseira estilo "S/el"/cushion) — seção "Perfil de Compra & Regras".
- Regra do Omega MoonSwatch: abaixo de R$1.500 no OLX é muito provavelmente réplica; faixa alvo de compra R$1.500–2.200.
- Metodologia de pesquisa (2nd Street ordenado por mais recentes, filtro de preço no OLX, sinais de alerta de vendedor) — seção "Metodologia de Pesquisa".
