---
title: "Exercício 2 - Agente de nuvem do GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lição anterior: Instruções personalizadas][previous-lesson] |
|:--|

É provável que poucas organizações não enfrentem problemas de dívida técnica. Ela pode incluir problemas de segurança não resolvidos, código legado que precisa de atualização ou solicitações de funcionalidades que ficaram no backlog por falta de tempo. O agente de nuvem do GitHub Copilot foi criado para tarefas como atualizar código e adicionar funcionalidades de forma autônoma. Quando termina, ele cria um pull request (PR) de rascunho para que uma pessoa desenvolvedora revise o trabalho. Assim, tarefas repetitivas podem ser delegadas e os desenvolvedores podem se concentrar em atividades de maior impacto.

Neste exercício, você explorará:

- a personalização do ambiente de geração de código.
- como garantir que as operações sejam executadas com segurança.
- a importância de problemas com escopo claro.
- a atribuição de problemas ao Copilot.

## Cenários

A Tailspin Toys quer resolver alguns problemas técnicos. Os prestadores de serviço que criaram a primeira versão do site não documentaram o código. Como primeiro passo, a equipe quer comentários TypeScript Documentation (TSDoc) em todas as funções exportadas do aplicativo.

A equipe de design também quer melhorar a descoberta de jogos. Ela deseja exibir jogos relacionados — outros títulos da mesma categoria — na página de detalhes de cada jogo, para explorar a experiência e fazer testes de aceitação. Ainda não é necessária uma implementação refinada. Essa tarefa está bloqueando o trabalho, mas há problemas de maior prioridade.

Esses são exemplos de tarefas que podem perder prioridade. O agente de nuvem do GitHub Copilot pode trabalhar nelas de forma assíncrona enquanto você se concentra em outras atividades e, depois, revisa o resultado.

## Apresentação do agente de nuvem do GitHub Copilot

O [agente de nuvem do GitHub Copilot](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) executa tarefas em segundo plano de forma semelhante a uma pessoa desenvolvedora. Você pode, por exemplo, [atribuir um problema do GitHub ao Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Depois da atribuição, o Copilot cria um PR de rascunho para acompanhar o progresso, configura um ambiente e começa a trabalhar. Você pode examinar a sessão durante a execução ou depois. Quando a solução estiver pronta para revisão, o Copilot mencionará você no pull request.

## A importância de instruções com escopo adequado

O GitHub Copilot não é mágico: você ainda precisa pensar no problema. Mesmo tarefas aparentemente simples podem ter complexidades ocultas. Por isso, [defina com cuidado o escopo das tarefas para o agente de nuvem][cloud-agent-best-practices]. Trate-o como um parceiro de programação com IA: trabalhe em etapas, aprenda, experimente e adapte. Os fundamentos do desenvolvimento de software não mudam com a IA generativa.

## Instruções personalizadas neste repositório

Os exercícios anteriores apresentaram as instruções personalizadas e como elas orientam o Copilot. Se você já concluiu o exercício prático de instruções personalizadas, viu `.github/copilot-instructions.md` em ação. Caso contrário, confira rapidamente os arquivos incluídos neste repositório antes de atribuir trabalho ao agente de nuvem. Isso ajudará você a identificar a influência das instruções posteriormente.

Para revisar o contexto do projeto antes de atribuir trabalho, abra os arquivos a seguir na interface Web do GitHub ou em um Codespace.

1. Abra `.github/copilot-instructions.md` e examine a seção **Code standards**, especialmente as convenções de TypeScript e as expectativas para comentários TSDoc.
2. Abra `.github/instructions/unit-tests.instructions.md` e observe o frontmatter `applyTo`, que limita essas instruções aos arquivos `**/*.test.ts`.

Ao atribuir o problema `Code lacks documentation` ao agente de nuvem na próxima seção, procure comentários TSDoc, convenções TypeScript e cabeçalhos de comentário no pull request resultante. Essas orientações vêm dos arquivos de instruções e serão retomadas em uma lição futura.

## Configure o ambiente de desenvolvimento do agente de nuvem do Copilot

Criar código costuma exigir um ambiente específico e scripts de configuração. O mesmo vale para tarefas do Copilot, que trabalha de maneira semelhante a uma pessoa engenheira de software.

O agente de nuvem usa o [GitHub Actions][github-actions] como ambiente de trabalho. Você pode personalizar o ambiente com um [fluxo de configuração especial][setup-workflow], definido em `.github/workflows/copilot-setup-steps.yml`, que é executado antes do início do trabalho. Essa configuração fornece as ferramentas e dependências necessárias. O laboratório já a preparou para que o agente tenha acesso ao Node.js, às dependências do projeto, aos navegadores do Playwright para testes de ponta a ponta e ao banco de dados SQLite migrado e preenchido do aplicativo Astro:

```yaml
name: "Copilot Setup Steps"

# Permite testar as etapas de configuração na guia "Actions" do repositório
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Defina as permissões mínimas necessárias para estas etapas. O Copilot recebe seu próprio token para as operações dele.
    permissions:
      # Para clonar o repositório nas etapas de configuração, por exemplo, para instalar dependências, será necessária a permissão `contents: read`. Se você não clonar o repositório, o Copilot fará isso automaticamente depois que as etapas forem concluídas.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Configuração do aplicativo: Node.js (o aplicativo agora é Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migra e preenche o banco de dados SQLite local para que builds e testes tenham dados.
      - name: Set up the database
        run: npm run db:setup
```

O arquivo se parece com qualquer fluxo de trabalho do GitHub, mas tem alguns pontos importantes:

- Há um único trabalho chamado `copilot-setup-steps`, executado no GitHub Actions antes de o Copilot começar a trabalhar no pull request.
- O gatilho `workflow_dispatch` permite executar o fluxo manualmente na guia **Actions** do repositório. Isso é útil para verificar se ele funciona sem esperar uma execução do Copilot.

## Adicione documentação

Embora todos reconheçam a importância da documentação, muitos projetos têm informações desatualizadas ou nenhuma documentação. Essa dívida técnica pode reduzir a produtividade e dificultar a manutenção ou a integração de novos desenvolvedores. O Copilot pode ajudar a criar documentação, por isso este é um bom problema para atribuir ao agente de nuvem. Em um exercício futuro, você voltará para revisar o trabalho.

1. Abra uma nova guia do navegador e acesse o repositório no github.com.
2. Selecione a guia **Issues**.
3. Selecione **New issue** para abrir a caixa de diálogo de novo problema.
4. Selecione **Blank issue**.
5. Defina **Title** como `Code lacks documentation`.
6. Defina **Description** como:

    ```plaintext
    Nossa organização exige que funções e métodos tenham comentários TSDoc quando úteis. Infelizmente, as atualizações recentes não seguiram esse padrão. Precisamos atualizar o código existente para incluir comentários de documentação quando eles esclarecerem o comportamento.
    ```

7. Selecione **Create** para criar o problema.
8. À direita, selecione **Assign to Copilot** para abrir a caixa de diálogo de atribuição.

  ![Atribuição do Copilot a um problema](../../../_images/shared-assign-copilot.png)

9. Selecione **Assign**.
10. Selecione a guia **Pull Requests**.
11. Abra o novo PR, com título semelhante a `[WIP]: Code lacks documentation`. Se ele ainda não aparecer, aguarde um ou dois minutos e atualize a página.
12. Depois de alguns minutos, o Copilot deverá criar uma lista de tarefas.

> [!NOTE]
> A lista pode levar alguns minutos para aparecer. O Copilot está preparando o ambiente, executando o fluxo de trabalho mencionado anteriormente, analisando o projeto e definindo uma abordagem.

13. Revise a lista e as tarefas planejadas.
14. Role a linha do tempo do PR para baixo e procure uma atualização indicando que o Copilot começou a trabalhar no problema.
15. Selecione **View session**.

  ![Visualização da sessão do Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Talvez seja necessário atualizar a janela para ver o indicador atualizado.

16. Percorra a sessão ao vivo e acompanhe como o Copilot resolve o problema: ele explora o código, entende o estado atual, define uma abordagem e cria código.

Isso pode levar alguns minutos. Como o agente de nuvem trabalha de forma assíncrona, você pode aproveitar esse recurso atribuindo outra tarefa e, em seguida, desenvolvendo uma funcionalidade.

## Adicione uma seção de jogos relacionados à página de detalhes

Uma das vantagens do agente de nuvem do GitHub Copilot é dividir o trabalho: você pode se concentrar em uma tarefa enquanto o agente trabalha em outra. Mesmo que a seção de jogos relacionados não leve muito tempo, você pode usar esse tempo em outra atividade. Atribua a tarefa ao agente de nuvem.

1. Retorne ao repositório no github.com.
2. Selecione a guia **Issues**.
3. Selecione **New issue**.
4. Selecione **Blank issue**.
5. Defina **Title** como `Show related games on the game details page`.
6. Defina **Description** como:

    ```markdown
    Queremos ajudar as pessoas a descobrir mais jogos, exibindo jogos relacionados na página de detalhes de cada jogo. A equipe de design quer explorar a experiência e fazer testes de aceitação. Os requisitos são:

   - Adicionar um auxiliar de acesso a dados em `src/lib/` que retorne outros jogos da mesma categoria de um jogo, excluindo o próprio jogo
   - Exibir uma seção "Related games" na página de detalhes do jogo usando o auxiliar
   - Tratar o caso em que um jogo não tenha jogos relacionados
   - Criar testes unitários para o novo auxiliar
   - Confirmar que todos os testes passam antes de criar o PR
    ```

7. Selecione **Create** para criar o problema.
8. À direita, selecione **Assign to Copilot** para abrir a caixa de diálogo de atribuição.

  ![Atribuição do Copilot a um problema](../../../_images/shared-assign-copilot.png)

9. Selecione **Assign**.

Pouco depois, você deverá ver 👀 no primeiro comentário do problema, indicando que o Copilot começou a trabalhar.

![Emoji de olhos indicando que o Copilot está trabalhando no problema](../../../_images/ex4-issue-eyes-emoji.png)

O Copilot agora está trabalhando na segunda solicitação. Como o agente de nuvem trabalha de forma semelhante a uma pessoa engenheira de software, não é necessário monitorá-lo continuamente. Revise o resultado quando estiver pronto. Enquanto isso, vamos criar e usar agentes personalizados.

## Resumo e próximos passos

Este exercício apresentou o [agente de nuvem do GitHub Copilot][copilot-agents]. Você pode atribuir problemas ao Copilot para execução assíncrona e usá-lo para resolver dívida técnica, criar funcionalidades ou migrar código entre frameworks.

Você explorou:

- a personalização do ambiente de geração de código.
- como garantir que as operações sejam executadas com segurança.
- a importância de problemas com escopo claro.
- a atribuição de problemas ao Copilot.

Enquanto o agente de nuvem trabalha, vamos criar e usar agentes personalizados. O [agente de nuvem também pode usar servidores Model Context Protocol (MCP)][cloud-agent-mcp] e instruções personalizadas, que já foram abordados.

## Recursos

- [Sobre o agente de nuvem do Copilot][copilot-agents]
- [Atribuir problemas do GitHub ao Copilot][assign-issue]
- [Práticas recomendadas para o fluxo de configuração do agente de nuvem][cloud-agent-best-practices]

| [Próxima lição: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
