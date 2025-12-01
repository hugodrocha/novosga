<!-- Orientações geradas para agentes de código (IA) trabalhando no NovoSGA -->
# NovoSGA — Instruções para Copilot / Agentes de IA

Objetivo: orientar rapidamente um agente de IA sobre a arquitetura do repositório, fluxos de trabalho e convenções específicas para garantir contribuições corretas e acionáveis.

- Visão geral: o NovoSGA é uma aplicação Symfony 7.x em PHP composta pelo projeto principal (`/src`) e por diversos bundles de domínio instalados via Composer (`novosga/*`). A lógica de negócio costuma ficar em `src/Service`, `src/Entity`, `src/Repository`; eventos e mensagens assíncronas ficam em `src/Message` e `src/MessageHandler`.

- Diretórios-chave e exemplos:
  - `src/`: código da aplicação (namespace PSR-4 `App\`). Veja `src/Kernel.php` para registro de bundles.
  - `config/`: configurações de serviços e rotas. Ex.: `config/packages/messenger.yaml` configura o Symfony Messenger.
  - `migrations/` e `migrations/sql/`: migrations do Doctrine; procure arquivos `Version*.php` e SQL específicos por SGBD em `migrations/sql/`.
  - `public/`: assets públicos e entrada `index.php`.
  - `bin/console` e `bin/phpunit`: utilitários para comandos e testes.
  - `tests/`: testes PHPUnit (veja `tests/bootstrap.php`).

- Organização do código (observações práticas):
  - Bundles de domínio (ex.: `novosga/attendance-bundle`) são dependências Composer; prefira estender/registrar serviços em `src/` e `config/` em vez de alterar código em `vendor/`.
  - Entidades usam Doctrine ORM. Repositórios ficam em `src/Repository` e o mapeamento segue o configurado em `config/packages/doctrine.yaml`.
  - Processamento assíncrono usa Symfony Messenger — mensagens em `src/Message` e handlers em `src/MessageHandler`.

- Fluxos de trabalho comuns (comandos concretos):
  - Instalar dependências: `composer install` (ou executar dentro do container Docker).
  - Subir stack com Docker: há `docker-compose.yaml` na raiz e `novosga/compose.yaml`. Exemplo:

```bash
docker compose -f docker-compose.yaml up -d
docker compose -f novosga/compose.yaml up -d
```

  - Tarefas do Symfony Console: `./bin/console cache:clear`, `./bin/console doctrine:migrations:migrate`, `./bin/console doctrine:migrations:diff`.
  - Rodar testes: `./bin/phpunit` (configurado via `phpunit.xml.dist`).
  - Consumidor do Messenger: `./bin/console messenger:consume async -vv`.

- Convenções e padrões do projeto:
  - Projeto exige PHP 8.2+ e usa tipagem moderna; siga PSR-12. Há configurações e regras em `phpcs.xml.dist` e `slevomat`.
  - Use o namespace `App\` para código novo em `src/` e registre serviços em `config/services.yaml` quando necessário.
  - Migrations são comitadas em `migrations/` e incluem SQL por SGBD em `migrations/sql/` — gere via console e comite as migrations.
  - Muitas funcionalidades são fornecidas por bundles externos (`novosga/*`) — consulte `composer.json` para saber o que é implementado fora do repositório.

- Pontos de integração e dependências externas:
  - OAuth2: `league/oauth2-server-bundle` (veja `config/packages/league_oauth2_server.yaml`) e migrations relacionadas.
  - Mercure para atualizações em tempo real: ver `config/packages/mercure.yaml` e scripts/JS em `public/js`.
  - Configuração de transports e workers do Messenger em `config/packages/messenger.yaml`; variáveis de ambiente podem alterar os transports.

- Testes & CI:
  - `tests/bootstrap.php` prepara o ambiente de testes; use `./bin/phpunit` localmente.
  - PHPStan está configurado em `phpstan.dist.neon` — execute `vendor/bin/phpstan analyse` quando necessário.

- Exemplos rápidos de referência:
  - Nova entidade Doctrine: adicionar classe em `src/Entity`, repositório em `src/Repository`, executar `./bin/console doctrine:migrations:diff` e `./bin/console doctrine:migrations:migrate`.
  - Nova tarefa assíncrona: criar mensagem em `src/Message`, handler em `src/MessageHandler`, despachar via Messenger e verificar transport em `config/packages/messenger.yaml`.

- O que NÃO alterar sem confirmação:
  - Código em `vendor/` ou bundles `novosga/*` — prefira extensão/configuração em `src/` ou `config/`.
  - Arquivos e segredos de produção nos manifests Docker — altere apenas templates `.env` salvo autorização.

Se algo estiver impreciso ou você quiser exemplos concretos (ex.: migration curta, exemplo de `Message`/`Handler` ou um teste), diga qual área quer que eu expanda que atualizo o arquivo.
