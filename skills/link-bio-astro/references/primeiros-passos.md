# Preparar o ambiente com um iniciante

Leia quando faltarem ferramentas ou experiência com terminal. Não peça para instalar tudo por padrão: o runtime do agente pode já fornecer Node, Git e acesso ao GitHub.

## Onde executar e quem faz

Identifique o agente/editor usado e se ele pode editar arquivos, executar terminal e acessar browser. No editor, use o terminal integrado; fora dele, Terminal no macOS/Linux e PowerShell ou Prompt de Comando no Windows. Explique que os comandos são executados no terminal, não na barra do navegador. Escolha uma pasta de trabalho com o usuário, sem sobrescrever outra aplicação. Forneça caminhos reais e entre na pasta que contém `package.json` antes dos comandos do app.

Se o agente consegue executar comandos, execute-os. Caso contrário, entregue um comando por vez, diga o resultado esperado e aguarde a resposta. Em Windows, não forneça comandos exclusivos de bash; adapte ao shell. Não é necessário instalar um segundo editor se o atual já funciona.

## Verificar antes de instalar

Execute separadamente:

```sh
node --version
npm --version
git --version
```

Uma versão retornada confirma presença, não compatibilidade. Confira os requisitos atuais do Astro escolhido e do Wrangler. Use uma versão LTS de Node suportada por ambos; registre essa versão em `.nvmrc` e alinhe o ambiente de build. Não reinstale uma versão compatível já disponível.

- **Node/npm ausente:** abra https://nodejs.org/en/download, selecione o sistema e um instalador oficial de uma LTS compatível. Oriente baixar, executar o instalador e reabrir terminal/editor. O npm acompanha a instalação usual de Node. Confira as versões novamente. No Linux, siga as instruções oficiais para a distribuição; se já usa um gerenciador de versões, reaproveite-o. Não prescreva `sudo npm` como correção de permissões.
- **Git ausente:** use https://git-scm.com/downloads e siga a opção oficial do sistema, depois reabra o terminal e confira a versão. Git controla versões; uma conta GitHub não instala Git no computador. Com ferramenta de repositório disponível, o agente pode enviar arquivos sem exigir uma CLI Git local.
- **GitHub CLI (`gh`):** é opcional. Prefira a ferramenta GitHub já conectada. Quando a CLI simplificar a publicação, use a instalação oficial em https://cli.github.com, confirme `gh --version` e faça login pelo navegador com `gh auth login`. Não peça token no chat nem mude a configuração Git global do usuário.

Se a skill ainda não estiver instalada, após Node/npm funcionar use `npx skills@latest add mutgarth/skills --skill link-bio-astro`, escolha o agente e confira a conclusão. Se o agente ainda não a reconhecer, siga o mecanismo de recarga dele. Não reexecute a instalação de uma skill já carregada.

## Criar ou reutilizar o app

Se já houver `package.json`, leia-o e mantenha gerenciador/lockfile. Em um projeto novo, no diretório pai escolhido, o agente pode usar o assistente oficial:

```sh
npm create astro@latest
```

Escolha uma pasta nova, o template mínimo e instalação de dependências. Explique as perguntas do assistente conforme aparecerem. Depois entre na pasta criada. Se a instalação foi pulada, rode `npm install`. O agente cria `src/content.json`, a página e os assets conforme a identidade escolhida; não deixe a pessoa preenchendo boilerplate.

No diretório do app, instale a CLI Cloudflare como dependência de desenvolvimento:

```sh
npm install -D wrangler
npx wrangler --version
```

Wrangler é a ferramenta de terminal da Cloudflare. Não precisa ser instalado globalmente. Preserve versões compatíveis e o lockfile. **Instalar Wrangler não exige fazer login na Cloudflare.** No caminho principal, o painel conecta o GitHub e o build remoto executa Wrangler com sua própria autenticação.

Abra a prévia com `npm run dev` e a URL exata exibida no terminal. `localhost`/`127.0.0.1` só serve para testar nessa máquina; ainda não é o link da bio. Pare o processo com Ctrl+C quando necessário. Rode `npm run build`: deve concluir sem erro e gerar `dist/`. Verifique o resultado também com `npm run preview` antes de publicar.

Se uma publicação manual local for realmente necessária, depois da conta criada e da autorização:

```sh
npx wrangler login
npx wrangler whoami
npm run deploy
```

O usuário conclui OAuth no navegador; `whoami` deve confirmar a conta esperada. Não copie o arquivo de credenciais. Esse caminho não substitui a conexão GitHub/Workers Builds descrita em [publicacao.md](publicacao.md).

## Se a preparação falhar

- `node`/`npm` não encontrado: confirme instalação e abra um terminal novo; investigue PATH antes de reinstalar.
- PowerShell bloqueia `npm.ps1`: use `npm.cmd`/`npx.cmd` ou Prompt de Comando; não desative a política de segurança global.
- `package.json` não encontrado: confira o diretório atual e entre na pasta real do app.
- Node incompatível: use uma LTS suportada e refaça a instalação de dependências, sem apagar arquivos do usuário.
- Autenticação abre no ambiente remoto: explique em qual máquina o comando roda; use fluxo de autenticação oficial compatível ou o painel, sem solicitar tokens no chat.

Se não houver ambiente capaz de criar/buildar o app, entregue os arquivos e oriente abrir a pasta em um agente/editor com terminal. Não prometa concluir a publicação a partir de um chat sem ferramentas.

Fontes: [Astro](https://docs.astro.build/en/install-and-setup/), [Wrangler](https://developers.cloudflare.com/workers/wrangler/install-and-update/), [Node](https://nodejs.org/en/download), [Git](https://git-scm.com/downloads), [GitHub CLI](https://cli.github.com).
