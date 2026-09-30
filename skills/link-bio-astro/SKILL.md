---
name: link-bio-astro
description: Crie e publique uma página pessoal de link na bio com Astro estático, conteúdo editável, GitHub e Cloudflare Workers Static Assets, incluindo domínio próprio e deploy automático após merge na main. Use também para atualizar páginas criadas com esse fluxo.
---

# Link na bio com Astro

Leve a pessoa da ideia à página publicada, adaptando a linguagem ao seu conhecimento técnico e idioma. Use Astro estático, GitHub e Cloudflare Workers Static Assets com Workers Builds. O resultado deve incluir uma página personalizada, conteúdo fácil de editar e publicação automática da branch `main`.

## Conduzir quem está começando

Primeiro descubra o agente/editor e o sistema operacional, se a pessoa consegue abrir uma pasta de projeto e se já tem contas. Inspecione ferramentas disponíveis antes de pedir que instale algo. Para preparar ambiente ou explicar terminal, leia [references/primeiros-passos.md](references/primeiros-passos.md).

Assuma o trabalho técnico que suas ferramentas permitem: arquivos, configuração, comandos e diagnóstico. A pessoa escolhe conteúdo/visual, faz login e autoriza acessos. Se só houver chat, explique essa limitação e oriente o próximo passo executável; não alegue ter criado arquivos ou publicado nada.

Conduza uma etapa por vez: diga **onde fazer, qual ação e qual resultado esperar**. Confira pelo terminal/browser quando tiver acesso; caso contrário peça o resultado ou mensagem de erro sem dados sensíveis. Não despeje o guia inteiro, não peça “configure o DNS” sem explicar e não avance por suposição. Termos como repositório (pasta do projeto no GitHub), build (gerar os arquivos do site), branch (versão separada) e merge (incorporar uma alteração) devem ser explicados na primeira utilização.

O caminho principal é **prévia local → GitHub → Workers Builds → endereço público → domínio escolhido → primeira atualização por PR**. Não exige deploy manual nem login local do Wrangler. Se a pessoa não tem domínio, ofereça começar em workers.dev e conectar um depois. Mantenha na conversa o último passo confirmado e as URLs criadas para retomar sem repetir recursos.

## Descobrir sem transformar em formulário

Aproveite o que a conversa já informou. Pergunte em pequenos grupos somente o que falta; continue trabalho independente enquanto espera.

- Qual nome/@ deve aparecer e qual frase curta acompanha o nome?
- Quais links, títulos e redes sociais entram? Qual ação deve receber mais destaque e em que ordem? Pergunte especificamente sobre comunidade, produto ou contato prioritário. Peça URLs reais; não deduza perfis a partir do nome.
- Tem foto, logo, ilustração, vídeo ou outro criativo que quer usar? Tem uma página de referência, paleta ou estilo preferido? Se não tiver, proponha uma direção simples; ofereça alternativas visuais quando houver dúvida, sem impor três layouts a todo usuário.
- Já tem conta no GitHub e na Cloudflare? Em qual conta/organização ficará o repositório, com qual nome e visibilidade? Explique que repositório público expõe código e assets; privado também funciona.
- Tem domínio próprio e acesso ao registrador/DNS? Qual endereço final deseja: domínio raiz ou subdomínio como `links.exemplo.com`? Já existe site ou e-mail nesse domínio?

Explique que **para publicar no endereço próprio é necessário possuir ou registrar o domínio**. Registro tem custo separado da hospedagem. Não diga que domínio pago é requisito técnico da Cloudflare: `workers.dev` permite começar sem ele. O fluxo principal termina no domínio próprio; se a pessoa preferir o endereço gratuito, respeite essa escolha e não bloqueie o projeto. Não compre domínio ou plano sem autorização específica.

## Construir a página

Inspecione o projeto existente antes de criar outro. Em projeto novo, use Astro com saída estática e versões compatíveis de Node/Astro/Wrangler; consulte documentação atual quando necessário. Não adicione SSR, adapter Cloudflare, banco, autenticação ou painel administrativo para uma lista estática. Se o usuário pedir admin, explique primeiro a edição pelo GitHub e avalie a necessidade real sem ignorar a solicitação.

Mantenha poucas peças:

- `src/content.json`: nome, frase curta, redes e lista ordenada de links. Cada link pode ter título, URL, logo e destaque. A ordem dos dados deve controlar a ordem visual; não esconda a comunidade em uma posição fixa no template.
- `src/pages/index.astro`: página principal, componentes só se reduzirem repetição real.
- `public/`: assets locais, logos e favicon.
- Configuração Wrangler e lockfile; instruções curtas no README do projeto para editar, executar e publicar.

Crie conteúdo curto, mobile-first, com ícones de redes identificáveis e uma ação principal evidente. Use os criativos fornecidos e logos reais de fontes oficiais quando disponíveis. Não copie a identidade, os perfis, o planeta ou os domínios de outro usuário como valores padrão. Sem assets, faça uma composição adequada ou proponha geração de imagem com ferramentas disponíveis.

Garanta navegação por teclado, foco visível, nomes acessíveis para ícones, contraste e movimento reduzido quando houver animação. Coloque título, descrição, favicon e metadados de compartilhamento coerentes com o endereço final. Imagens grandes devem ser otimizadas para o uso real.

Use campos de texto escapados pelo Astro; não injete HTML cru vindo do JSON. Valide URLs: links web usam HTTPS; contato pode usar `mailto:` ou `tel:` quando escolhido. Rejeite protocolos executáveis e não publique URLs fictícias ou botões aparentemente ativos sem destino.

Mostre uma prévia local antes de publicar quando o visual ainda não foi aceito. Verifique build, links/destinos, assets, favicon e layout estreito. Use um pequeno check de conteúdo/build quando útil; não crie uma suíte extensa para uma página simples.

## Publicar e automatizar

Leia [references/publicacao.md](references/publicacao.md) ao entrar na etapa de contas, repositório, domínio ou deploy. Ela contém o caminho operacional e as verificações de automação.

A autorização para usar esta skill não é autorização genérica para criar contas, aceitar termos, comprar serviços, tornar código público, substituir DNS ou fazer merge. Reaproveite autorizações claras já dadas no pedido e na conversa; não peça de novo. Prepare a página e a configuração para revisão antes de solicitar qualquer aprovação que ainda seja necessária. Login, verificações de e-mail, MFA e consentimento OAuth ficam com o usuário; nunca peça senhas ou tokens no chat.

Se não houver CLI, API ou browser apropriado, guie a pessoa pelo painel em etapas pequenas e continue após a confirmação do resultado. Não finja que a integração foi configurada. Em falha, inspecione o erro e o estado antes de repetir: não crie Workers/repos duplicados. Se a próxima tentativa exigir permissão ou alteração de escopo, pare essa operação, explique o bloqueio e siga apenas com trabalho independente.

## Entregar e manter

Entregue URL pública, repositório, nome do Worker, arquivo de conteúdo e caminho de atualização: **editar → branch → PR → merge na main → build Cloudflare → site atualizado**. Explique onde roda: arquivos gerados pelo Astro são servidos pela Cloudflare, sem VPS ou computador pessoal ligado.

Diferencie o que foi verificado do que falta. Só diga que deploy automático funciona depois de observar um build disparado pelo Git e a versão publicada correspondente; deploy manual não demonstra essa integração.

Em atualizações posteriores, preserve o estilo e a infraestrutura existentes, altere os dados/assets necessários, valide e use o fluxo Git configurado. Faça merge/publicação quando autorizado; caso contrário entregue o PR pronto. Para rollback durável, reverta o commit e faça novo merge; um rollback apenas no painel pode ser sobrescrito pelo próximo deploy.
