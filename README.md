# Template Next.js: ESLint, Prettier e Code Runner

Este projeto serve como referência e ponto de partida para outros projetos. A base utiliza Next.js, React, TypeScript e Tailwind CSS, com ESLint para analisar código, Prettier para padronizar a formatação e Code Runner para executar classes e scripts TypeScript no VS Code.

## 1. Preparar o ambiente

Instale o Node.js em uma versão compatível com o Next.js declarado em [package.json](package.json), com npm disponível. Abra a **pasta raiz do projeto** no VS Code.

### Instalar as extensões

O arquivo [.vscode/extensions.json](.vscode/extensions.json) recomenda estas extensões:

| Extensão | Identificador | Função |
| --- | --- | --- |
| Prettier | `esbenp.prettier-vscode` | Formatar arquivos no editor. |
| ESLint | `dbaeumer.vscode-eslint` | Mostrar problemas encontrados pelo ESLint. |
| Code Runner | `formulahendry.code-runner` | Executar arquivos pelo editor. |

Abra **Extensions** (`Ctrl+Shift+X`), pesquise `@recommended` e instale as recomendações deste workspace. O VS Code também pode oferecer essa instalação ao abrir a pasta. O arquivo de recomendações não instala as extensões automaticamente.

### Instalar as dependências

No terminal, na raiz do projeto, execute:

```sh
npm install
```

Para adicionar todas as dependências de ESLint, Prettier e execução de TypeScript a outro projeto Next.js, copie esta linha (com as versões declaradas neste template):

```sh
npm install -D eslint@^9 eslint-config-next@16.3.4 prettier@^3.9.6 eslint-config-prettier@^10.1.8 typescript@^5 tsx@^4.23.13 @types/node@^20
```

O `-D` salva os pacotes em `devDependencies`. Esse comando pressupõe que o projeto já tenha Next.js instalado; mantenha a versão de `eslint-config-next` alinhada à versão do Next.js do projeto. O Code Runner é uma extensão do VS Code, não um pacote npm. Para instalar todas as dependências da aplicação deste template, incluindo React e Tailwind CSS, use `npm install` com o `package.json` fornecido.

As extensões e os pacotes npm são complementares: instalar uma extensão não instala as dependências do projeto. As principais ferramentas já estão declaradas em `package.json`:

- `eslint`: analisa o código conforme as regras configuradas.
- `eslint-config-next`: fornece as configurações para Next.js e TypeScript.
- `prettier`: formata o código.
- `eslint-config-prettier`: desativa regras que conflitam com o Prettier.
- `typescript`: fornece o compilador e a verificação de tipos.
- `tsx`: executa TypeScript no Node.js sem compilação manual prévia.

Para instalações reproduzíveis, como na integração contínua, use `npm ci` quando `package-lock.json` estiver atualizado e compatível com `package.json`. Versione o lockfile para compartilhar as versões resolvidas.

## 2. Configurar ESLint sem conflito com Prettier

O [eslint.config.mjs](eslint.config.mjs) utiliza o formato **flat config** do ESLint. A extensão `.mjs` identifica um módulo JavaScript com `import` e `export`.

O ESLint analisa o código segundo suas regras; o Prettier padroniza sua apresentação. Há conflito quando uma regra de estilo exige, por exemplo, aspas ou indentação diferentes das aplicadas pelo Prettier. Formatar pode gerar um aviso, e corrigir pelo ESLint pode desfazer a formatação.

O projeto já evita essa disputa com `eslint-config-prettier`, conforme a [documentação do Prettier](https://prettier.io/docs/integrating-with-linters). A configuração existente é:

```js
import { defineConfig, globalIgnores } from 'eslint/config';
import nextVitals from 'eslint-config-next/core-web-vitals';
import nextTs from 'eslint-config-next/typescript';
import prettier from 'eslint-config-prettier/flat';

const eslintConfig = defineConfig([
  ...nextVitals,
  ...nextTs,
  prettier,
  globalIgnores(['.next/**', 'out/**', 'build/**', 'next-env.d.ts']),
]);

export default eslintConfig;
```

Coloque `prettier` depois das configurações que definem regras, para desativar as conflitantes. O `globalIgnores` seguinte apenas define exclusões. Se adicionar configurações com regras de estilo, mantenha `prettier` depois delas.

**`eslint-config-prettier` não executa o Prettier.** A formatação acontece pela extensão ou pelo comando npm. Os diretórios gerados e `next-env.d.ts` são ignorados pelo ESLint.

Para analisar o código:

```sh
npm run lint
```

Para aplicar as correções automáticas disponíveis:

```sh
npm run lint -- --fix
```

Nem todo problema tem correção automática. A configuração atual do editor não inclui correção automática do ESLint ao salvar.

## 3. O que é `.prettierrc`?

O [.prettierrc](.prettierrc) define as preferências de formatação do projeto, utilizadas no editor e na linha de comando:

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "all"
}
```

| Opção | Efeito |
| --- | --- |
| `semi` | Adiciona ponto e vírgula ao final das instruções. |
| `singleQuote` | Prefere aspas simples onde aplicável. JSON mantém aspas duplas, e JSX possui uma opção própria. |
| `tabWidth` | Define a largura da indentação como dois espaços. |
| `trailingComma` | Adiciona vírgulas finais em estruturas com várias linhas quando a sintaxe permitir. |

As demais opções seguem os padrões da versão instalada do Prettier.

Para formatar os arquivos suportados:

```sh
npm run format
```

Para verificar sem alterar os arquivos:

```sh
npm run format:check
```

Se precisar excluir arquivos da formatação, crie um `.prettierignore`. As exclusões do ESLint não configuram as exclusões do Prettier.

## 4. O que é a pasta `.vscode`?

A pasta [.vscode](.vscode) guarda configurações do VS Code específicas deste projeto. Versioná-la permite compartilhar o ambiente de edição. As configurações de workspace têm precedência sobre as configurações gerais correspondentes, respeitando as regras de configuração por linguagem do editor. Veja a [documentação do VS Code](https://code.visualstudio.com/docs/configure/settings).

Neste template ela contém:

- **`extensions.json`**: recomenda as extensões.
- **`settings.json`**: configura a formatação e o Code Runner.

O [.vscode/settings.json](.vscode/settings.json) contém:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "code-runner.clearPreviousOutput": true,
  "code-runner.runInTerminal": true,
  "code-runner.ignoreSelection": true,
  "code-runner.executorMap": {
    "typescript": "clear ; npx tsx"
  }
}
```

| Configuração | Função |
| --- | --- |
| `editor.defaultFormatter` | Seleciona Prettier como formatador padrão. |
| `editor.formatOnSave` | Formata arquivos suportados ao salvar. |
| `code-runner.clearPreviousOutput` | Limpa a saída anterior no canal de saída da extensão. Neste projeto, a execução usa o terminal, cuja tela é limpa pelo comando `clear`. |
| `code-runner.runInTerminal` | Executa no terminal integrado. |
| `code-runner.ignoreSelection` | Executa o arquivo inteiro, mesmo com texto selecionado. |
| `code-runner.executorMap` | Define o comando usado para executar TypeScript. |

Outros projetos podem incluir `launch.json` para depuração e `tasks.json` para tarefas automatizadas. Esses arquivos não estão presentes aqui.

## 5. Executar classes e scripts com Code Runner

Depois de instalar as extensões e dependências, crie, por exemplo, `scripts/exemplo.ts`:

```ts
class Saudacao {
  constructor(private readonly nome: string) {}

  executar(): void {
    console.log(`Olá, ${this.nome}!`);
  }
}

const saudacao = new Saudacao('mundo');
saudacao.executar();
```

Declarar a classe não chama seus métodos: o exemplo instancia a classe e chama `executar()`.

Salve o arquivo, mantenha-o ativo e use **`Ctrl+Alt+N`**, o menu de contexto **Run Code** ou a paleta (`F1` → **Run Code**). A saída aparece no terminal integrado. Para interromper a execução nesse terminal, use `Ctrl+C`. Consulte os comandos na [documentação do Code Runner](https://github.com/formulahendry/vscode-code-runner#usages).

O Code Runner acrescenta o caminho do arquivo ao executor configurado. Aqui, `clear` limpa o terminal e `npx tsx` executa TypeScript com o pacote instalado localmente.

**Compatibilidade do terminal:** `clear ; npx tsx` funciona no PowerShell e em shells compatíveis com essa sintaxe. No Prompt de Comando (`cmd.exe`), altere o executor para `cls & npx tsx`, ou use apenas `npx tsx` para dispensar a limpeza.

Também é possível executar sem a extensão, a partir da raiz:

```sh
npx tsx scripts/exemplo.ts
```

Use esse fluxo para scripts que funcionam no Node.js. Componentes React, páginas Next.js e código que depende de APIs do navegador devem executar no contexto da aplicação.

O `tsx` executa TypeScript sem verificar os tipos. Para verificar os tipos, execute:

```sh
npx tsc --noEmit
```

## 6. Comandos disponíveis

| Comando | Finalidade |
| --- | --- |
| `npm run dev` | Iniciar o servidor de desenvolvimento. |
| `npm run build` | Gerar a versão de produção. |
| `npm start` | Servir a versão de produção após o build. |
| `npm run lint` | Analisar o código com ESLint. |
| `npm run format` | Aplicar a formatação. |
| `npm run format:check` | Verificar a formatação sem modificar arquivos. |

Após `npm run dev`, acesse o endereço mostrado no terminal, normalmente [http://localhost:3000](http://localhost:3000). A página inicial fica em `src/app/page.tsx`.

## 7. Reutilizar o template

1. Copie ou utilize o repositório como template, preservando `.vscode`, `.prettierrc`, `eslint.config.mjs`, `package.json` e `package-lock.json`.
2. Ajuste o nome em `package.json` e atualize o lockfile com `npm install`.
3. Instale as extensões recomendadas e as dependências.
4. Adapte as regras e o executor ao ambiente do novo projeto.
5. Confira com `npm run lint` e `npm run format:check`.

Ao reaproveitar apenas as ferramentas em um projeto sem Next.js, substitua as importações de `eslint-config-next` por configurações adequadas ao novo ambiente, mantendo a integração com `eslint-config-prettier`.

## Problemas comuns

- **Não formata ao salvar:** confira se Prettier está instalado e habilitado, se a pasta raiz foi aberta e se alguma configuração da linguagem seleciona outro formatador.
- **ESLint não mostra avisos:** confira a extensão e as dependências; consulte o canal ESLint no painel Output do VS Code.
- **Code Runner não encontra Node.js ou npx:** confira a instalação e o `PATH`. Reabra o VS Code após instalar o Node.js.
- **Erro no comando de limpeza:** escolha PowerShell como terminal padrão ou adapte o executor conforme explicado acima.
- **A classe não produz saída:** salve o arquivo e confira se o método desejado é chamado.
