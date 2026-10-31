# Segundo Cérebro com Obsidian + GitHub (skill para agentes)

Uma skill que ensina o seu agente de IA a montar, do zero, um **segundo cérebro** no Obsidian,
organizado pelo método **PARA** e sincronizado por um repositório **privado** no GitHub entre o
servidor do agente, o celular e o computador.

Feita e testada pela **RenixAI** num cofre real, com uma pessoa iniciante, guiada por print.

## O que a skill faz

1. Monta o cofre no servidor: pastas PARA (Entrada, Projetos, Áreas, Recursos, Arquivo, Diário),
   modelos de nota, projeto, área e revisão semanal.
2. Sobe o cofre para um repositório privado no GitHub, conferindo antes que nada sensível vai junto.
3. Guia a pessoa, passo a passo e com telas prontas, a ligar o Obsidian no **celular** (plugin Git)
   e no **computador** (Windows ou Mac).
4. Personaliza, se a pessoa quiser: tema, ícones nas pastas e cores do gráfico.
5. Traz as armadilhas que já aconteceram (conflitos do Git, Vim aberto, celular sem sincronizar)
   e como sair de cada uma.

## Para quem é

- Feita para agentes **DG Claw**: usa a lousa do painel (telas com passo a passo e botão Copiar)
  e o cofre de chaves. Nesse caso funciona direto.
- Em outro agente (Claude Code puro, por exemplo), também dá: troque a lousa por instruções em
  texto e guarde o token do GitHub numa variável de ambiente `GITHUB_TOKEN`.

## Instalação

1. Baixe este repositório: botão verde **Code** › **Download ZIP**, ou
   ```bash
   git clone https://github.com/<usuario>/<repositorio>.git
   ```
2. Copie a pasta `skills/montar-segundo-cerebro` para a pasta de skills do seu agente:
   ```bash
   cp -r skills/montar-segundo-cerebro ~/.claude/skills/
   ```
3. Reinicie a sessão do agente.
4. Peça: **"monta um segundo cérebro pra mim"**.

## O que tem aqui

```text
skills/montar-segundo-cerebro/
  SKILL.md                 a receita completa, etapa por etapa
  base/
    COMO-FUNCIONA.md       explicação do cofre para a pessoa
    _modelos/              nota, projeto, área, revisão semanal
    icones-pastas.css      ícones nas pastas do Obsidian
    graph.json             cores e espaçamento do gráfico
  lousas/
    vis-obsidian-celular.html     passo a passo do celular
    vis-obsidian-computador.html  passo a passo do computador
    vis-obsidian-puxar.html       como puxar as atualizações
```

Os campos `{{NOME}}`, `{{EMAIL}}`, `{{USUARIO}}`, `{{REPO}}` e `{{ASSISTENTE}}` são preenchidos
pelo agente com os dados de cada pessoa.

## Segurança

- O agente **nunca pede chave ou token no chat**. A chave do celular a pessoa cola direto no Obsidian.
- O repositório do cofre é **privado** e fica na conta GitHub **da própria pessoa**.
- Antes do primeiro envio, o agente confere o histórico e tira documento sensível (RG, assinatura,
  laudo) pelo `.gitignore`.
- Nunca grave o token dentro do `remote` do Git nem em arquivo do cofre.

## Licença

MIT. Pode usar, adaptar e compartilhar, mantendo o aviso de licença.

---

Feito por **RenixAI**. Melhorou a skill? Abre uma issue ou um pull request.
