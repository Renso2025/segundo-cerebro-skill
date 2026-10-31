---
name: montar-segundo-cerebro
description: Montar um segundo cérebro do zero (Obsidian + GitHub privado) pra uma pessoa, sincronizando entre o servidor, o celular e o computador. Use quando o dono pedir "criar um segundo cérebro", "montar o Obsidian pra alguém", "segundo cérebro pra cliente", "configurar o Obsidian no celular/computador", "sincronizar o Obsidian com o GitHub", "fazer igual fizemos com o meu", ou oferecer o serviço de segundo cérebro. Pro dia a dia do cofre (anotar, organizar, buscar), a skill é segundo-cerebro.
---

# Montar um segundo cérebro do zero

Receita testada num cofre real (out/2026), generalizada pra qualquer pessoa
(o próprio dono, família ou cliente). Leva umas 2 a 3 horas com a pessoa, guiada por print.

## Antes de começar: pegue os dados (pergunte só o que faltar)

| Variável | O que é | Exemplo |
|---|---|---|
| `{{NOME}}` | nome que assina as notas | Ana |
| `{{EMAIL}}` | e-mail do GitHub da pessoa | (pergunte) |
| `{{USUARIO}}` | usuário do GitHub | ana-silva |
| `{{REPO}}` | repositório privado do cofre | Segundo_Cerebro |
| `{{ASSISTENTE}}` | nome do agente que cuida do cofre | a Luna |
| aparelhos | Android, iPhone, Windows, Mac | Android + Windows |
| áreas | as áreas da vida dela (viram pastas em 02-Areas) | Família, Finanças, Faculdade |

Regras que valem do começo ao fim:
- **Nunca peça chave/token no chat.** A chave do celular a pessoa cola direto no Obsidian. Se a gente
  precisar de chave no servidor, é pelo bloco `dgcofre`.
- **A pessoa é iniciante:** uma lousa por aparelho, passos numerados, onde clicar, e "me manda print"
  no fim de cada etapa. Texto pra copiar sempre em caixinha com botão Copiar.
- **Cliente que não é o dono:** o cofre fica no GitHub DA PESSOA, com o login dela. Eu não guardo
  token de cliente. Combine com o dono quem faz a parte do servidor antes de começar.

## Etapa 1: montar o cofre (eu, no servidor)

1. Crie a pasta (pro dono, `~/segundo-cerebro`; pra cliente, `~/clientes/<nome>/segundo-cerebro`).
2. Estrutura PARA: `00-Entrada`, `01-Projetos`, `02-Areas`, `03-Recursos`, `04-Arquivo`, `05-Diario`,
   `_modelos`, `_anexos`. Copie `base/_modelos/` e `base/COMO-FUNCIONA.md` (troque {{ASSISTENTE}} pelo
   nome certo). Crie `Início.md` com o mapa das pastas e uma nota por área que a pessoa citou.
3. `.gitignore` com pelo menos:
   ```
   .obsidian/workspace*.json
   .trash/
   ```
   e **qualquer documento sensível** (assinatura, RG, laudo) que deva ficar só no servidor.
4. `git init -b main`, `git add -A`, primeiro commit.

## Etapa 2: o GitHub privado

1. A pessoa cria o repositório **privado** `{{REPO}}`, vazio (sem README), e manda print.
2. Antes do primeiro push, confira se não tem nada sensível no histórico:
   `git log --all --name-only --format= | sort -u`. Se tiver, tire do histórico
   (`git filter-branch --index-filter 'git rm --cached --ignore-unmatch <arquivo>' -- --all`, depois
   `git reflog expire --expire=now --all && git gc --prune=now`) e ponha no `.gitignore`.
3. Push (com o token do cofre e sem guardar credencial):
   ```bash
   U="https://x-access-token:${GITHUB_TOKEN}@github.com/{{USUARIO}}/{{REPO}}.git"
   git -c credential.helper= push -q "$U" HEAD:main 2>&1 | sed "s/$GITHUB_TOKEN/***/g"
   ```
   Nunca grave a URL com token no `remote` nem imprima o token.
4. Peça um print do repositório no GitHub mostrando as pastas.

## Etapa 3: celular (lousa `lousas/vis-obsidian-celular.html`)

Copie a lousa pra `~/visuais/`, troque `{{USUARIO}}`, `{{REPO}}`, `{{EMAIL}}`, `{{NOME}}` e mande.
Passos: criar cofre vazio "Neste dispositivo", plugin **Git** (autor Vinzent), chave fine-grained
(Only select › `{{REPO}}`, Contents: Read and write, 1 ano), username + token no plugin, comando
**Git: Clone an existing remote repo** (pasta Vault Root, depth em branco), reabrir o Obsidian,
ligar **Pull on startup** e **Auto commit-and-sync 10 min**.

Teste: peça pra pessoa criar uma nota "Teste" e sincronizar. Confira o commit chegando
(`git pull`) e confira que a chave **não** foi parar no repositório:
`grep -ri "github_pat\|ghp_" .obsidian/` tem que voltar vazio.

## Etapa 4: computador (lousa `lousas/vis-obsidian-computador.html`)

Windows: Git for Windows (Next em tudo), PowerShell com `git config --global user.name/email`,
`git clone` em `Documents\Segundo Cerebro` (login pelo navegador, o Git Credential Manager guarda,
**sem chave nova**), abrir a pasta como cofre, "Confiar no autor e ativar plugins".
Mac: mesmo caminho com o Terminal (`xcode-select --install` traz o git).

Teste: a pessoa escreve na nota de teste e roda **Git: Commit-and-sync**. Eu respondo na mesma
nota e dou push, pra ela ver a volta. Ciclo fechado = sync completo.

## Etapa 5: personalização (opcional, pergunte o gosto)

- Tema **Minimal** (confira o `theme.css` antes: sem `@import` de fora), claro ou escuro, cor de destaque.
- Ícones nas pastas: `base/icones-pastas.css` em `.obsidian/snippets/` e ligado em `appearance.json`
  (`enabledCssSnippets`). Acrescente uma linha por pasta de área ou projeto da pessoa.
- Cores do gráfico: `base/graph.json` (grupos por pasta, forças já espaçadas pra os nomes não
  sobreporem: centerStrength 0.3, repelStrength 18, linkStrength 0.6, linkDistance 250, scale 0.8).
- Depois do push, a pessoa puxa (lousa `lousas/vis-obsidian-puxar.html`) e manda print.

## Armadilhas que já aconteceram

- **Conflito "deleted by us"**: a pessoa apagou uma nota no PC enquanto eu editava. Saída:
  `git rm "<arquivo>"; git commit --no-edit`. Prevenção: não edito nota que ela pode estar mexendo.
- **Conflito add/add no `.obsidian/graph.json`**: o Obsidian cria esse arquivo sozinho quando o
  gráfico é aberto. Saída: `git checkout --theirs .obsidian/graph.json; git add .obsidian/graph.json;
  git commit --no-edit; git push`. Prevenção: personalizar ANTES de abrir o cofre no computador, ou
  pedir pra fechar o Obsidian e rodar `git checkout -- .obsidian; git pull`.
- **Git abriu o Vim** (tela preta com `~`) no commit de merge: digitar `:wq` e Enter.
- **"Pulling is not possible because you have unmerged files"**: rodar `git status`, mandar print,
  resolver arquivo por arquivo como acima.
- **Tema e ícones não aparecem no PC**: quase sempre o pull travou num conflito. `git status` primeiro.
- **Celular sem sincronizar**: abrir o Obsidian e esperar uns segundos antes de escrever (Pull on startup).

## No fim

- Mande um resumo curto: onde fica o cofre, como sincroniza, e a regra de ouro (abrir, esperar
  puxar, depois escrever).
- Anote no diário do dia e, se for o dono ou família, na memória (onde fica o repo).
- Se a pessoa for usar comigo no dia a dia, a skill `segundo-cerebro` cuida da rotina do cofre.
