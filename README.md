# Skills do souomeneses

Skills que nascem dos projetos que construo com IA. Instruções práticas para transformar uma ideia em algo funcionando — e que você pode adaptar ao seu jeito.

## Instalar

Escolha as skills e o agente em que quer instalar:

```bash
npx skills@latest add mutgarth/skills
```

Para instalar apenas a skill de link na bio:

```bash
npx skills@latest add mutgarth/skills --skill link-bio-astro
```

## Skills disponíveis

| Skill | O que faz |
| --- | --- |
| [link-bio-astro](skills/link-bio-astro/SKILL.md) | Cria uma página de link na bio com Astro, ajuda a configurar GitHub, Cloudflare e domínio próprio, e publica atualizações após merge na `main`. |

### Link na bio

A skill pergunta quais links merecem destaque, quais redes devem aparecer e se você tem fotos, logos ou referências visuais. Depois orienta a construção e publicação da página, com os links organizados em um JSON fácil de editar.

Para usar no Codex após instalar:

```text
Use $link-bio-astro para criar meu link na bio e publicar no meu domínio.
```

O domínio próprio é necessário para ter um endereço personalizado. Você também pode começar com o endereço `workers.dev` fornecido pela Cloudflare. A skill orienta a criação das contas; login, autorizações e compras ficam com você.

## Atualizar

```bash
npx skills update
```

## Organização

Cada skill fica em `skills/<nome>/`, com um `SKILL.md` e apenas os recursos necessários para executar seu fluxo. Para adicionar uma nova, crie a pasta, escreva as instruções e adicione uma entrada ao catálogo acima.

As skills deste repositório são a versão publicada. Editar uma cópia instalada no computador não atualiza o GitHub automaticamente: traga a alteração para este repositório, revise e faça commit.

## Sobre

Criado por [souomeneses](https://links.omeneses.com). Troque ideias e compartilhe o que construiu na [comunidade Vibe Mode](https://discord.gg/czSvnQGNH).

Organização e distribuição inspiradas no repositório [mattpocock/skills](https://github.com/mattpocock/skills). As instruções desta coleção são próprias; não é um fork da coleção dele.

## Licença

[MIT](LICENSE). Use, adapte e compartilhe.
