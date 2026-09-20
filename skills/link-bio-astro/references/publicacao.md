# Publicação: GitHub + Cloudflare + domínio

Consulte a documentação oficial atual antes de executar configurações de provedor; menus, versões e limites podem mudar. Este fluxo usa Workers Static Assets e Workers Builds, não exige migração para Pages nem GitHub Actions.

## 1. Contas e acesso

Se faltar GitHub, guie a criação em https://github.com/signup e a verificação de e-mail. Se faltar Cloudflare, guie https://dash.cloudflare.com/sign-up e a verificação de e-mail. Deixe o usuário preencher credenciais, aceitar termos e concluir MFA/OAuth diretamente. Retome depois sem pedir segredos no chat.

Confira a identidade/conta de destino antes de criar recursos. `gh auth status` pode verificar a sessão GitHub, quando disponível; use `gh auth login` se necessário. Para publicação manual inicial via CLI, use Wrangler login. A integração de builds tem autorização própria: login local do Wrangler não conecta o GitHub nem configura CI.

Verifique elegibilidade e limites atuais do plano gratuito de Workers/Builds; não prometa gratuidade ilimitada nem ative plano pago automaticamente. Domínio é cobrado separadamente.

## 2. Projeto e repositório

Use o gerenciador já adotado e commite o lockfile. Em projeto novo, npm é suficiente. Scripts mínimos de um Astro estático:

```json
{
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro preview",
    "deploy": "npm run build && wrangler deploy"
  }
}
```

Instale Astro e Wrangler em versões compatíveis e fixe a resolução no lockfile. Use Node suportado pela versão escolhida, alinhado entre ambiente local e build Cloudflare; não copie um número antigo sem conferir.

Um `wrangler.jsonc` para arquivos estáticos tem `name` escolhido para este projeto, `compatibility_date` válida (data atual ao criar) e `assets.directory` igual a `./dist`. Exemplo ilustrativo, a personalizar:

```json
{
  "name": "minha-bio",
  "compatibility_date": "2026-09-20",
  "workers_dev": true,
  "assets": { "directory": "./dist" }
}
```

Não adicione entrypoint JS `main` ou adapter SSR para servir somente esses arquivos. Quando o domínio estiver pronto, acrescente a configuração persistente:

```json
"routes": [{ "pattern": "links.exemplo.com", "custom_domain": true }]
```

Substitua o hostname pelo escolhido, sem protocolo, caminho ou wildcard. O nome do Worker no painel deve corresponder a `name` na configuração.

Antes do primeiro push, confira os arquivos rastreados. Exclua `.env`, credenciais, `node_modules`, `dist`, `.astro` e `.wrangler`. Dados/arquivos servidos pelo site serão públicos mesmo se o repositório for privado. Não copie tokens de configuração local.

Com nome, conta e visibilidade definidos e publicação autorizada, crie o repositório via ferramenta GitHub, `gh repo create` ou painel. Use `main` como branch de produção e envie o projeto. Preserve um repositório existente em vez de reinicializar ou sobrescrever histórico.

## 3. Conectar o GitHub ao Worker

No painel **Workers & Pages**, importe o repositório GitHub para um novo Worker, ou abra o Worker existente em **Settings → Builds → Connect**. O nome dos menus pode mudar; confirme no painel/docs atuais.

O usuário autoriza o app Cloudflare no GitHub, preferindo acesso só ao repositório escolhido. Escolha:

| Campo | Valor |
| --- | --- |
| Repositório | Conta e repo confirmados |
| Branch de produção | `main` |
| Diretório raiz | Raiz do app Astro; `/` se o repo inteiro for o app |
| Build | `npm run build` |
| Deploy de produção | `npx wrangler deploy` |

Se houver um check real disponível, acrescente `&& npm test` ao build; não configure scripts inexistentes. Como o painel já executa o build, não use um script de deploy que o execute novamente. Use Wrangler do projeto/lockfile para não trocar de versão a cada execução.

Para branches não produtivas, desative builds se não forem necessários ou use o mecanismo de previews `npx wrangler versions upload`; não use deploy de produção nelas. Confira autenticação de build pelo mecanismo do provedor, sem commitar tokens.

**Merge na main gera um push na main e dispara o deploy. Push direto na main também dispara.** Oriente branch + PR para o fluxo pedido. Se o usuário quiser obrigar revisão antes de publicar, configure proteção/ruleset compatível com a conta, quando autorizado; não declare que proteção existe só porque foi recomendada.

## 4. Domínio próprio

Se ainda não houver domínio, ajude a escolher/registrar um com custo e renovação claros, deixando a compra com o usuário. Não é necessário transferir o registro para a Cloudflare; para este caminho de Custom Domains é necessário ter a zona ativa nessa conta Cloudflare.

Adicione o domínio raiz à Cloudflare (por exemplo, `exemplo.com`, mesmo quando o site usará `links.exemplo.com`). Se já estiver ativo, reutilize. Caso ainda use outro DNS:

1. Revise/importe os registros existentes antes da troca. Preserve site, MX, SPF, DKIM, DMARC e verificações; a detecção automática pode não encontrar tudo.
2. Explique que a troca de nameservers afeta o domínio inteiro. Confirme autorização para essa mudança se o pedido não a cobrir.
3. Guie a configuração dos nameservers exatos atribuídos pela Cloudflare no registrador. Se DNSSEC estiver ativo, siga o procedimento atual de migração de DS/DNSSEC, sem improvisar.
4. Aguarde a zona ficar ativa e confira resolução. Não considere espera concluída apenas por tempo decorrido.

Configure **Custom Domain** do Worker para o hostname escolhido e mantenha o mesmo valor em `wrangler.jsonc`. Cloudflare gerencia DNS/certificado desse vínculo. Um CNAME já existente nesse hostname pode impedir a criação: investigue o uso antes de remover/substituir. Não altere raiz, e-mail ou outros subdomínios para resolver uma colisão no endereço escolhido.

## 5. Provar que está funcionando

- Build local passa e os assets necessários estão em `dist`.
- Primeiro build Cloudflare conclui e o endereço HTTPS abre com favicon, imagens e links corretos.
- Verifique o domínio próprio e certificado sem ignorar erros TLS. Se estiver pendente, informe exatamente o estado.
- Com autorização para merge, use uma alteração pequena e útil já revisada, por exemplo completar as instruções de manutenção, em uma branch/PR; faça merge na `main`. Não introduza texto temporário no site só para testar.
- Observe um build disparado pelo Git, correlacione SHA da `main`, status de sucesso e deployment ativo. Para mudança visual/de conteúdo, confira também o resultado público. Não execute deploy manual para mascarar falha da integração.
- Sem autorização de merge ou sem acesso ao painel, entregue o PR/passo pendente e descreva a automação como configurada mas ainda não validada, conforme evidências.

No README do app, documente o arquivo de links, edição pela interface GitHub criando branch/PR, merge, acompanhamento do build e rollback por revert. Para colocar na bio da rede social, indique copiar a URL HTTPS final e colar no campo de site/link do perfil; só edite perfis externos se solicitado.

## Fontes oficiais

- [Conta Cloudflare](https://developers.cloudflare.com/fundamentals/account/create-account/)
- [Adicionar domínio e revisar DNS](https://developers.cloudflare.com/fundamentals/manage-domains/add-site/)
- [Migração de nameservers](https://developers.cloudflare.com/dns/zone-setups/full-setup/setup/)
- [Astro e Cloudflare](https://docs.astro.build/en/guides/deploy/cloudflare/)
- [Workers Static Assets](https://developers.cloudflare.com/workers/static-assets/)
- [Workers Builds](https://developers.cloudflare.com/workers/ci-cd/builds/)
- [Configuração dos builds](https://developers.cloudflare.com/workers/ci-cd/builds/configuration/)
- [Branches e previews](https://developers.cloudflare.com/workers/ci-cd/builds/build-branches/)
- [Custom Domains](https://developers.cloudflare.com/workers/configuration/routing/custom-domains/)
- [Endereço workers.dev](https://developers.cloudflare.com/workers/configuration/routing/workers-dev/)
