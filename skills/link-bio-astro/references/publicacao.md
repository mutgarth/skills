# Publicação: GitHub + Cloudflare + domínio

Consulte a documentação oficial atual antes de executar configurações de provedor; menus, versões e limites podem mudar. Este fluxo usa Workers Static Assets e Workers Builds, não exige migração para Pages nem GitHub Actions.

Em cada etapa abaixo, mostre apenas o próximo passo necessário e o sinal de sucesso. Os nomes dos botões estão em inglês; use o equivalente da interface atual. Se a tela divergir, inspecione-a ou peça à pessoa que descreva as opções, sem inventar cliques. Ambiente ainda não preparado: leia [primeiros-passos.md](primeiros-passos.md).

## 1. Contas e acesso

**GitHub:** abra https://github.com/signup; peça ao usuário que conclua o cadastro e confirme o e-mail. Se já tem conta, use Sign in. Resultado esperado: conseguir abrir o perfil autenticado e identificar a conta onde ficará o projeto.

**Cloudflare:** abra https://dash.cloudflare.com/sign-up; peça ao usuário que conclua o cadastro e confirme o e-mail recebido. Se já tem conta, use Log in. Resultado esperado: painel da conta acessível. Se o onboarding pedir um domínio, explique que para começar em workers.dev ele pode ir a Workers & Pages; não compre um domínio só para passar dessa tela.

Credenciais, aceite de termos, MFA e consentimentos OAuth são concluídos pelo usuário diretamente. Retome do ponto confirmado sem pedir segredos no chat. Não interprete a abertura da tela de cadastro como conta criada.

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

Siga [primeiros-passos.md](primeiros-passos.md) para instalar Astro/Wrangler e verificar a prévia. Fixe a resolução das dependências no lockfile. Use Node suportado pela versão escolhida, alinhado entre ambiente local e build Cloudflare; não copie um número antigo sem conferir.

Um `wrangler.jsonc` para arquivos estáticos tem `name` escolhido para este projeto, `compatibility_date` válida (data atual ao criar) e `assets.directory` igual a `./dist`. Exemplo ilustrativo, a personalizar:

```json
{
  "name": "minha-bio",
  "compatibility_date": "2026-09-30",
  "workers_dev": true,
  "assets": { "directory": "./dist" }
}
```

Não adicione entrypoint JS `main` ou adapter SSR para servir somente esses arquivos. Não acrescente domínio pendente ao primeiro deploy: isso pode bloqueá-lo. Quando a zona estiver ativa e o endereço autorizado, acrescente a configuração persistente:

```json
"routes": [{ "pattern": "links.exemplo.com", "custom_domain": true }]
```

Substitua o hostname pelo escolhido, sem protocolo, caminho ou wildcard. O nome do Worker no painel deve corresponder a `name` na configuração.

Antes do primeiro push, confira os arquivos rastreados. Exclua `.env`, credenciais, `node_modules`, `dist`, `.astro` e `.wrangler`. Dados/arquivos servidos pelo site serão públicos mesmo se o repositório for privado. Não copie tokens de configuração local.

Com nome, conta e visibilidade definidos e publicação autorizada, crie o repositório via ferramenta GitHub, `gh repo create` ou painel. Use `main` como branch de produção e envie o projeto. Preserve um repositório existente em vez de reinicializar ou sobrescrever histórico.

Se usar `gh`, após `gh auth status` confirmar a conta correta, o agente cria o commit e o repositório com nome/visibilidade escolhidos. Exemplo para uma pasta nova já revisada (substitua conta/nome e a visibilidade antes de executar):

```sh
git init -b main
git add .
git commit -m "Create link in bio page"
gh repo create CONTA/NOME --private --source . --remote origin --push
```

Não execute esse exemplo inteiro em um repo existente. Se faltar identidade para commit, peça nome/e-mail a usar e configure apenas nesse repositório; não invente e-mail.

Sem ferramenta GitHub/CLI, acompanhe pelo site: **+ → New repository → Owner → Repository name → Public/Private → Create repository**. Para receber um projeto local existente, deixe desmarcada a criação de README/licença/gitignore nessa tela. O agente envia o projeto usando Git autenticado disponível; não peça senha GitHub no terminal. Se faltar autenticação, use `gh auth login` ou o fluxo oficial do GitHub Desktop. Como alternativa para esta página pequena, **uploading an existing file** (ou **Add file → Upload files**) permite enviar os arquivos previamente selecionados pelo agente: incluir `src`, `public`, configurações, lockfile e `.gitignore`; excluir `node_modules`, `dist` e credenciais. Confira que `package.json` ficou na raiz, não dentro de uma pasta extra.

**Sinal de sucesso:** abra a URL do repo e confira a branch `main`, `package.json`, lockfile, `src/`, `public/` e `wrangler.jsonc`. Não avance com repo vazio ou push que falhou.

## 3. Conectar o GitHub ao Worker

O caminho principal começa pelo painel, sem exigir deploy manual anterior:

1. Na conta Cloudflare correta, abra **Workers & Pages → Create application**.
2. Em **Import a repository**, selecione **Get started** e a opção GitHub, conforme a tela atual.
3. Se a conta GitHub não estiver conectada, o usuário conclui a instalação/autorização do app Cloudflare no GitHub. Oriente **Only select repositories** e o repo criado, quando essa opção existir.
4. De volta à Cloudflare, escolha a conta Git e o repositório. Se não aparecer, confira a conta e o acesso do app antes de criar outro repo.
5. Preencha/revise os campos da tabela abaixo; depois selecione **Save and Deploy**.
6. Acompanhe o build no painel. Ao concluir, abra a URL HTTPS `workers.dev` exibida. Se a conta pedir um subdomínio workers.dev, ajude a escolher um disponível; use a URL retornada, não uma URL deduzida.

Se o Worker já existe, use **Workers & Pages → nome do Worker → Settings → Builds → Connect**, selecione repo/branch e configure os mesmos campos. Não crie outro Worker para contornar um erro de conexão.

O usuário autoriza o app Cloudflare no GitHub, preferindo acesso só ao repositório escolhido. Escolha:

| Campo | Valor |
| --- | --- |
| Nome do Worker | Exatamente o `name` do wrangler.jsonc |
| Repositório | Conta e repo confirmados |
| Branch de produção | `main` |
| Diretório raiz | Raiz do app Astro; `/` se o repo inteiro for o app |
| Build | `npm run build` |
| Deploy de produção | `npx wrangler deploy` |

Se houver um check real disponível, acrescente `&& npm test` ao build; não configure scripts inexistentes. Como o painel já executa o build, não use um script de deploy que o execute novamente. Use Wrangler do projeto/lockfile para não trocar de versão a cada execução.

Para um Worker novo neste fluxo inicial, deixe builds de branches não produtivas desativados quando o painel oferecer essa opção. Em Worker existente, preserve a configuração de previews salvo pedido de mudança. Se o usuário quiser previews, consulte a configuração atual; não configure `wrangler deploy` de produção para branches de trabalho. Confira autenticação de build pelo mecanismo do provedor, sem commitar tokens. Login Wrangler local não fornece credenciais ao build remoto.

**Sinal de sucesso:** build concluído, deployment ativo e página pública com o conteúdo esperado. Uma URL de preview ou apenas um upload de versão não comprova deploy de produção. Se houver erro, abra os logs em **Deployments → View build history** (ou o caminho equivalente atual), identifique a primeira falha relevante e use o diagnóstico abaixo.

**Merge na main gera um push na main e dispara o deploy. Push direto na main também dispara.** Oriente branch + PR para o fluxo pedido. Se o usuário quiser obrigar revisão antes de publicar, configure proteção/ruleset compatível com a conta, quando autorizado; não declare que proteção existe só porque foi recomendada.

## 4. Domínio próprio

Se ainda não houver domínio, ajude a escolher/registrar um com custo e renovação claros, deixando a compra com o usuário. Não é necessário transferir o registro para a Cloudflare; para este caminho de Custom Domains é necessário ter a zona ativa nessa conta Cloudflare.

Explique antes: domínio é o endereço; registrador é onde ele foi comprado; DNS indica para onde cada endereço aponta; nameservers definem quem administra esse DNS. Uma zona ativa significa que o domínio está usando a configuração esperada pela Cloudflare.

No painel da conta, procure **Add a domain / Onboard a domain** (em algumas interfaces, **Add a site**). Informe o domínio raiz, por exemplo `exemplo.com`, mesmo que o site vá usar `links.exemplo.com`; avance e escolha o plano gratuito quando disponível e adequado. Revise a importação de DNS. Se o domínio já estiver ativo nessa conta, reutilize e pule a troca de nameservers. Caso ainda use outro DNS:

1. Abra o painel do provedor que administra o DNS atual (pode ser diferente do registrador) e sua lista de registros. Exporte a zona, se disponível, ou registre tipo, nome, valor, prioridade e TTL de todos os registros. Compare com a importação da Cloudflare e complete o que faltar. Preserve site, MX, SPF, DKIM, DMARC e verificações; consultas públicas isoladas não garantem inventário completo. Sem acesso a essa lista, não troque nameservers ainda.
2. Explique que a troca de nameservers afeta o domínio inteiro. Confirme autorização para essa mudança se o pedido não a cobrir.
3. Pergunte em qual registrador o domínio foi comprado. No painel dele, encontre a área do domínio e **Nameservers / Servidores DNS**; siga o guia atual desse registrador e substitua pelos nameservers exatos exibidos pela Cloudflare, após a revisão e autorização. Não invente um caminho de menu comum a todos os registradores. Se DNSSEC estiver ativo, siga o procedimento atual de migração de DS/DNSSEC, sem improvisar.
4. Aguarde a zona ficar ativa e confira resolução. Não considere espera concluída apenas por tempo decorrido.

Com zona ativa, vá em **Workers & Pages → seu Worker → Settings → Domains & Routes → Add → Custom Domain**. Digite o endereço escolhido (por exemplo `links.exemplo.com`, sem `https://`) e selecione **Add Custom Domain**. O agente mantém o mesmo valor em `wrangler.jsonc` e envia a alteração pelo fluxo Git. Cloudflare gerencia DNS/certificado desse vínculo. Um CNAME já existente nesse hostname pode impedir a criação: investigue o uso antes de remover/substituir. Não altere raiz, e-mail ou outros subdomínios para resolver uma colisão no endereço escolhido.

Se a pessoa preferiu workers.dev, pule toda esta etapa de domínio e entregue esse endereço como resultado válido. Se o domínio estiver pendente, ofereça o endereço temporário sem declarar concluído o domínio personalizado.

## 5. Primeira atualização acompanhada

Explique: branch é uma cópia de trabalho; PR (pull request) é uma proposta de mudança; merge incorpora essa mudança à versão publicada, `main`.

Faça com a pessoa uma alteração útil e aprovada, como trocar o título de um link. O agente valida o JSON, o build e os destinos. Quando ela quiser editar pelo navegador:

1. No repo GitHub, abra **src → content.json → Edit** (ícone de lápis). Mostre qual valor concreto trocar, preservando aspas e vírgulas.
2. Clique **Commit changes**, descreva a mudança e escolha **Create a new branch for this commit and start a pull request**. Use um nome simples, como `atualizar-links`.
3. Em **Create pull request**, confira que o destino é `main`. Revise **Files changed** e os checks disponíveis. O agente pode buscar a branch e validar o build local antes do merge; não presuma que existe CI no repo.
4. Quando autorizado e os checks necessários passarem, clique **Merge pull request → Confirm merge**. Se houver proteção ou conflito, explique o bloqueio e resolva dentro das permissões existentes.
5. Na Cloudflare, abra o histórico de builds e acompanhe o commit resultante do merge. Compare o SHA com a `main`, confirme o deployment ativo e recarregue o site público para ver a alteração.

Push direto em `main` também publica: a integração reage à mudança da branch, não exclusivamente ao botão de merge. Não use um deploy manual para “provar” esse teste. Se o build falhar, leia os logs e corrija por outro commit/PR; não afirme que a nova versão está no ar.

## 6. Provar que está funcionando

- Build local passa e os assets necessários estão em `dist`.
- Primeiro build Cloudflare conclui e o endereço HTTPS abre com favicon, imagens e links corretos.
- Quando houver domínio próprio, verifique-o e confira o certificado sem ignorar erros TLS. Se estiver pendente, informe exatamente o estado.
- Com autorização para merge, use uma alteração pequena e útil já revisada, por exemplo completar as instruções de manutenção, em uma branch/PR; faça merge na `main`. Não introduza texto temporário no site só para testar.
- Observe um build disparado pelo Git, correlacione SHA da `main`, status de sucesso e deployment ativo. Para mudança visual/de conteúdo, confira também o resultado público. Não execute deploy manual para mascarar falha da integração.
- Sem autorização de merge ou sem acesso ao painel, entregue o PR/passo pendente e descreva a automação como configurada mas ainda não validada, conforme evidências.

No README do app, documente o arquivo de links, edição pela interface GitHub criando branch/PR, merge, acompanhamento do build e rollback por revert. Para colocar na bio da rede social, indique copiar a URL HTTPS final e colar no campo de site/link do perfil; só edite perfis externos se solicitado.

## Quando o repositório não aparece

Em conta pessoal GitHub, abra **foto do perfil → Settings → Applications → Installed GitHub Apps → app Cloudflare → Configure**. Confira **Repository access**, mantenha o acesso limitado e selecione o repo desejado. A pessoa confirma a alteração. Em organização, use as configurações da organização e a seção **GitHub Apps**, ou peça ao administrador a aprovação necessária. Depois volte à Cloudflare e atualize a seleção de repositórios. Consulte o [guia oficial de apps instalados](https://docs.github.com/en/apps/using-github-apps/reviewing-and-modifying-installed-github-apps) se a interface diferir.

## Bloqueios frequentes

| Sintoma | Próxima ação |
| --- | --- |
| Repo não aparece na Cloudflare | Conferir conta/organização, autorização do app para esse repo e eventual aprovação do administrador; não pedir acesso a todos os repos como atalho. |
| Build não encontra package.json | Conferir diretório raiz e estrutura no GitHub. |
| Astro/Wrangler não encontrado | Conferir dependência e lockfile enviados, logs da instalação e se dependências de desenvolvimento foram omitidas. |
| Node incompatível no build | Alinhar uma LTS suportada localmente e na configuração do build; conferir a versão efetiva nos logs. |
| Nome do Worker não corresponde | Alinhar painel e `name` do Wrangler sem criar duplicata. |
| Falha de autenticação no build | Revisar a conexão/permissões do build na Cloudflare; repetir login local não corrige essa autorização. |
| Build passou, site antigo | Comparar branch/SHA, comando de deploy e deployment ativo; depois conferir a URL correta e cache. |
| 404 ou imagens ausentes | Conferir `dist/index.html`, `assets.directory`, arquivos em `public` e maiúsculas/minúsculas dos caminhos. |
| Domínio pendente ou CNAME conflitante | Conferir zona/nameservers e o uso do registro existente; não apagar DNS de outro serviço. |
| Erro de certificado HTTPS | Acompanhar emissão/ativação e diagnóstico Cloudflare; não ignorar aviso de segurança do navegador. |

Em falha de rede ou resultado incerto de criação/publicação, consulte o estado antes de repetir. Pare tentativas idênticas sem evidência nova. Preserve o projeto e informe o último passo confirmado e a única ação necessária para retomar.

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
