---
title: "Exercício 3 - Teste sua funcionalidade com o servidor Playwright do Model Context Protocol (MCP)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← Lição anterior: Modo de agente][previous-lesson] · [Próxima lição: Agentes personalizados →][next-lesson]

Você acabou de criar a funcionalidade de filtragem com o modo de agente. Antes de abrir um pull request, confirme que ela funciona no navegador. Em vez de testar o aplicativo manualmente, conecte o **servidor Playwright MCP** e deixe o Copilot controlar um navegador real. Depois, publique a branch e abra o PR.

Neste exercício, você vai:

- aprender o que é o Model Context Protocol (MCP) e como os servidores MCP ampliam as ferramentas do Copilot.
- adicionar o servidor Playwright MCP ao workspace.
- pedir ao Copilot que o use para testar manualmente a filtragem no navegador.
- publicar a branch e abrir um pull request para o trabalho de filtragem.

## O que é o Model Context Protocol (MCP)?

O modo de agente fica mais poderoso quando pode acessar recursos além do editor. O Model Context Protocol (MCP) permite que o Copilot faça isso: é um padrão para o agente se comunicar com ferramentas e serviços externos.

![Diagrama da arquitetura do modo de agente e sua interação com o contexto, o modelo de linguagem grande (LLM) e as ferramentas, incluindo ferramentas de servidores MCP e extensões do VS Code](../../../_images/ex1-mcp-diagram.png)

O [Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) permite que agentes de IA se comuniquem com ferramentas e serviços externos. Eles podem acessar informações atualizadas usando recursos e executar ações em seu nome usando ferramentas.

Um servidor MCP funciona como ponte entre o agente de IA e as ferramentas e serviços externos. Ele gerencia a comunicação entre o agente e ferramentas externas, como interfaces de programação de aplicações (APIs) existentes ou ferramentas locais em pacotes npm. Cada servidor MCP oferece um conjunto de ferramentas e recursos.

Alguns servidores MCP conhecidos:

- **[Servidor MCP do GitHub](https://github.com/github/github-mcp-server)**: oferece acesso a APIs para gerenciar repositórios do GitHub, criar repositórios, atualizar os existentes e gerenciar problemas e pull requests.
- **[Servidor Playwright MCP](https://github.com/microsoft/playwright-mcp)**: oferece automação de navegador com Playwright, como acessar páginas, preencher formulários e selecionar botões.

Há muitos outros servidores MCP. O GitHub mantém um [registro MCP](https://github.com/mcp) para facilitar a descoberta e as contribuições ao ecossistema.

> [!CAUTION]
> Por segurança, trate os servidores MCP como qualquer outra dependência do projeto. Antes de usar um servidor, revise o código-fonte, verifique o publicador e considere as implicações de segurança. Use apenas servidores confiáveis e tenha cuidado ao conceder acesso a recursos ou operações confidenciais.

## Revise a configuração MCP

O arquivo `.vscode/mcp.json` configura os servidores MCP disponíveis neste workspace do VS Code.

1. Abra `.vscode/mcp.json` no Codespace.
2. Observe que já há um servidor `github` configurado:

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

Essa entrada `github` vem do modelo do projeto e permitiu que o Copilot lesse o backlog no exercício anterior. Ela usa o [servidor MCP remoto do GitHub][remote-github-mcp-server]. Não é necessário instalar nada localmente: o VS Code conecta-se por HTTP, e você autentica-se no GitHub na primeira vez que o Copilot usa uma ferramenta.

## Adicione o servidor Playwright MCP

Agora, adicione um segundo servidor. O [servidor Playwright MCP][playwright-mcp-server] fornece ao Copilot um navegador controlável para testar a funcionalidade.

1. Em `.vscode/mcp.json`, adicione uma entrada `playwright` ao lado de `github`, para que o arquivo fique assim:

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. Salve o arquivo.

Ao contrário do servidor remoto `github`, `playwright` é um servidor **local**: o VS Code o inicia na máquina executando `npx @playwright/mcp@latest`. A opção `--headless` executa o navegador sem uma janela visível, o que é necessário no Codespace, que não tem uma área de trabalho para exibi-la.

> [!NOTE]
> O projeto Tailspin Toys já usa Playwright para testes de ponta a ponta, então o navegador necessário geralmente já está instalado. Se o Copilot informar que o navegador está ausente, peça que execute `npx playwright install chromium` e tente novamente.

## Inicie e confie no servidor

O VS Code inicia servidores MCP **sob demanda**: na primeira vez que o Copilot precisa das ferramentas de um servidor, o VS Code o inicia e pede que você confirme se confia nele.

1. Em `.vscode/mcp.json`, selecione a ação **Start** exibida acima da entrada `playwright` para iniciar o servidor e confirmar que a configuração está correta.
2. Se o VS Code pedir para confirmar que você confia no servidor, revise a configuração e autorize-o.

Iniciar o servidor manualmente é opcional. Se você ignorar essa etapa, o VS Code iniciará o servidor e pedirá que você confie nele quando o Copilot precisar das ferramentas.

## Teste a funcionalidade de filtragem

O [servidor Playwright MCP][playwright-mcp-server] fornece ao Copilot um navegador real. Em vez de testar o aplicativo manualmente, o agente pode abrir uma página, navegar, aplicar filtros, ler o resultado e resumir o que observou.

Nos bastidores, o servidor Playwright MCP usa a [árvore de acessibilidade][playwright-mcp-server], não capturas de tela. O agente raciocina sobre elementos estruturados e rotulados, como botões, links e itens de lista, de forma semelhante às tecnologias assistivas. Assim, uma verificação funcional rápida também ajuda a identificar problemas básicos de acessibilidade.

Com o servidor conectado e o aplicativo em execução, peça ao Copilot que teste a filtragem.

1. Envie este prompt:

   ```text
   Usando o servidor Playwright MCP, abra um navegador no aplicativo em execução em http://localhost:4321 e verifique a nova funcionalidade de filtragem de jogos:

   1. Acesse a página de jogos e informe quantos jogos estão listados.
   2. Aplique um filtro de categoria e confirme que a lista mostra somente os jogos dessa categoria.
   3. Limpe o filtro e aplique um filtro de editora. Confirme que a lista mostra somente os jogos dessa editora.
   4. Combine um filtro de categoria e um de editora e confirme que os resultados correspondem aos dois.

   Relate o que observou em cada etapa e indique qualquer comportamento inesperado.
   ```

O Copilot abrirá um navegador pelo servidor Playwright MCP, seguirá as etapas e relatará o que encontrou. Compare o resumo com os critérios de aceitação do problema. Se algo estiver errado, faça perguntas complementares ou peça ao Copilot que corrija o código antes de abrir o pull request.

> [!NOTE]
> O aplicativo precisa estar em execução em `http://localhost:4321`. Se o servidor de desenvolvimento estiver parado, inicie-o antes de enviar o prompt. Na primeira vez que o Copilot usar o Playwright MCP, talvez seja necessário baixar um navegador. Se o Copilot informar que o navegador está ausente, peça que execute `npx playwright install chromium` e tente novamente.

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## Publique a branch e crie um pull request

Depois de confirmar que a funcionalidade funciona, abra um pull request (PR) para que a equipe possa revisá-la. Primeiro, publique a branch `filtering-vscode`.

1. No Codespace, abra o painel **Source Control** e revise as alterações feitas pelo Copilot.
2. Prepare as alterações selecionando o ícone **+**.
3. Gere uma mensagem de commit usando o botão **Sparkle**.

   ![Painel Source Control com as alterações feitas](../../../_images/ex3-source-control-changes.png)

4. Selecione **Publish** para enviar a branch ao repositório.

## Crie o pull request

Há várias maneiras de criar um pull request, incluindo o github.com e a interface de linha de comando do GitHub. Como você já está usando o GitHub Copilot, peça que ele crie o PR, encontre o problema relacionado e associe os dois.

1. Abra o painel Copilot Chat e selecione **New Chat** para iniciar uma sessão.
2. No menu de agentes, confirme que **Agent** está selecionado para que o Copilot possa usar as ferramentas do GitHub.
3. Peça ao Copilot que crie o PR:

   ```plaintext
   Encontre no repositório o problema relacionado à filtragem por categoria e editora. Crie um pull request para a branch atual e associe-o ao problema correto.
   ```

4. Selecione **Continue** quando necessário para permitir que o Copilot reúna informações e execute operações. Na primeira vez que usar uma ferramenta do GitHub, talvez seja necessário autenticar-se.
5. Observe o Copilot pesquisar os problemas, encontrar o correto e criar o PR.
6. Selecione o link fornecido pelo Copilot para revisar o pull request, mas **não o mescle ainda**.

## Resumo e próximos passos

Neste exercício, você:

- aprendeu o que é o Model Context Protocol (MCP) e como os servidores MCP ampliam as ferramentas do Copilot.
- adicionou o servidor Playwright MCP ao workspace.
- usou o servidor para testar manualmente a filtragem no navegador antes de entregá-la.
- publicou a branch e abriu um pull request para a filtragem.

Você usou um servidor MCP para *testar* uma funcionalidade, mas MCP é apenas uma das maneiras de ampliar o Copilot. Em seguida, [crie um agente personalizado][next-lesson] para facilitar tarefas específicas, como revisões de acessibilidade.

## Recursos

- [O que é MCP e por que todos estão falando sobre ele?][mcp-blog-post]
- [Servidor Playwright MCP da Microsoft][playwright-mcp-server]
- [Servidor MCP do GitHub][github-mcp-server]
- [Registro MCP do GitHub][mcp-registry]
- [Servidores MCP no VS Code][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
