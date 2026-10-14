# Palma Skills

Conecte seu agente ao Palma e opere sua conta do Mercado Livre pelo chat.

## Instalar

Com Node.js e npm disponíveis, execute e escolha seu agente:

```sh
npx skills add ViniOcCode/palma-skills --skill palma-marketplace-workflow -g
```

Para instalar diretamente no Codex:

```sh
npx skills add ViniOcCode/palma-skills --skill palma-marketplace-workflow -g -a codex
```

Para instalar diretamente no Hermes:

```sh
npx skills add ViniOcCode/palma-skills --skill palma-marketplace-workflow -g -a hermes-agent
```

O [CLI skills](https://github.com/vercel-labs/skills) também instala em outros agentes compatíveis. Para operar o Palma, o agente precisa suportar MCP remoto por Streamable HTTP e OAuth.

## Conectar

Abra uma nova sessão do agente, se necessário. Substitua `[URL correta do MCP]` pela URL do serviço e envie:

> Use a skill do Palma para conectar minha conta do Mercado Livre. A URL correta do MCP é [URL correta do MCP]. Use esse endereço no lugar de <PALMA_MCP_PUBLIC_URL> em references/setup.md ao configurar o harness. Depois da autorização, confirme que minha conta está pronta para uso.

A URL termina em `/mcp`; o endereço deste repositório não é a URL do serviço. Os exemplos da referência mantêm `<PALMA_MCP_PUBLIC_URL>`; o agente substitui esse valor pela URL informada no prompt ao configurar a conexão. Se você ainda não tiver a URL, obtenha-a com o responsável pelo serviço antes de conectar.

A skill orienta o agente a configurar a conexão. Se ele não puder editar a configuração, fornecerá os passos para você executar. Conclua a autorização no navegador usando a conta titular do Mercado Livre. Depois, o agente verifica qual conta está conectada. Instalar a skill não configura o MCP nem concede acesso à sua conta.

As [instruções de conexão](skills/palma-marketplace-workflow/references/setup.md) incluem Codex, Hermes e recuperação de acesso. A [skill](skills/palma-marketplace-workflow/SKILL.md) inclui seleção de conta, prévias de alterações, tarefas e retomada após interrupções.

## Atualizar

```sh
npx skills update palma-marketplace-workflow -g
```

## Distribuição

Este repositório contém apenas instruções para o agente. O serviço Palma MCP é hospedado separadamente. Ele não contém o backend, credenciais ou tokens de contas.

As instruções de operação permanecem condicionadas às ferramentas e permissões disponíveis no agente e no serviço conectado. Instalação bem-sucedida não comprova uma conexão OAuth nem uma operação no Mercado Livre.
