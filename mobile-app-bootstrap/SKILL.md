---
name: mobile-app-bootstrap
description: Checklist de lições aprendidas para começar ou depurar qualquer app mobile Expo/React Native — versão do SDK, migrações de banco, formulários, e diagnóstico de travamentos. Use SEMPRE que o usuário pedir para criar um novo app mobile, escaffolding de projeto Expo/React Native, ou quando estiver depurando um bug estranho num app Expo/React Native existente (erro de Intl, crop de imagem, campo de valor monetário, app que "parou de funcionar" depois de um reload, banco SQLite com coluna faltando). Também use quando comandos do terminal (git, npm, expo start) começarem a travar sem razão aparente.
---

# Bootstrap e depuração de apps Expo/React Native

Este checklist nasceu da construção de um app real (finanças pessoais, Expo SDK 54,
SQLite local, 3 temas visuais) e captura os erros que mais custaram tempo/tokens
naquela sessão. O objetivo não é seguir cegamente — é evitar redescobrir os mesmos
problemas do zero.

## Antes de criar o projeto

**Descubra a versão do Expo Go instalada no aparelho de teste ANTES de rodar
`create-expo-app`.** Abra o app Expo Go no celular → aba Settings → "Supported SDK".
Crie o projeto usando essa mesma versão de SDK (ou rebaixe logo no início com
`npx expo install expo@<versão> -- --legacy-peer-deps`). Descobrir isso só depois
de já ter construído o app inteiro significa rebaixar dezenas de dependências e
retestar tudo — foi o maior desperdício de tempo da sessão que originou esta skill.

Se o Mac não tiver Xcode (só Command Line Tools), não existe simulador iOS — o
único jeito de testar é num aparelho físico via Expo Go, com as limitações abaixo.

## Ao instalar dependências

- Ambientes com Node mais antigo que o exigido pelo Expo mostram avisos
  `EBADENGINE` — geralmente inofensivos, mas confirme rodando `npx tsc --noEmit`.
- Conflitos de peer dependency em instalações Expo novas são comuns; use
  `npx expo install <pacote> -- --legacy-peer-deps` em vez de brigar com o npm.

## APIs que se comportam diferente do que a documentação da web sugere

O motor JS do React Native (Hermes) e as libs do Expo mudam de versão pra versão.
Sempre confira a doc da versão exata antes de usar, mas fique de olho especialmente em:

- **`Intl.NumberFormat.prototype.formatToParts` não existe no Hermes.** Se precisar
  só do símbolo da moeda, derive de `.format(0)` removendo dígitos/pontuação —
  não use `formatToParts`.
- **`expo-image-picker` com `allowsEditing: true` pode não mostrar tela de recorte**
  se o usuário deu apenas acesso "limitado" às fotos no iOS — nesse caso o sistema
  usa o PHPicker nativo, que não suporta edição, e ignora a opção silenciosamente.
  Não confie no recorte nativo para consistência visual: recorte a imagem você
  mesmo no código (`expo-image-manipulator`, crop central na proporção desejada)
  depois de receber a foto.
- **`expo-file-system` mudou de API baseada em strings (`documentDirectory`) para
  uma API baseada em classes (`Paths`, `File`, `Directory`)** nas versões recentes.
  Confira a versão instalada antes de escrever código de arquivo.

## Migrações de banco (SQLite ou similar)

Não confie só num contador de versão (`PRAGMA user_version` ou equivalente) para
decidir se uma migração já rodou. Durante o desenvolvimento, hot-reload e reinícios
de servidor podem dessincronizar esse contador da estrutura real da tabela. Prefira
uma checagem defensiva que roda toda vez que o app abre:

```ts
const columns = db.getAllSync("PRAGMA table_info(minha_tabela)");
if (!columns.some((c) => c.name === "coluna_nova")) {
  db.execSync("ALTER TABLE minha_tabela ADD COLUMN coluna_nova TEXT;");
}
```

Isso é barato de rodar sempre e se autocorrige, em vez de exigir diagnosticar por
que o contador de versão "mentiu".

## Ciclo de teste: Fast Refresh não é confiável para tudo

Depois de mudar lógica que não é puramente visual (schema de banco, hooks com
`useEffect([])`, módulos nativos), **peça um Reload completo** (sacudir o
aparelho → Reload, ou fechar e reabrir o Expo Go), não confie no hot-reload. Várias
vezes um "o bug continua" era só o aparelho rodando um pacote JS desatualizado.

Para confirmar rapidamente se o aparelho já está com o código novo sem adivinhar,
adicione um `console.log` temporário com uma tag única (ex: data/hora) no ponto
que você quer verificar, e confira nos logs do `expo start` se essa tag apareceu
depois do reload.

## Se comandos de terminal começarem a travar sem motivo

Antes de assumir que é um bug de código, rode:

```bash
uptime
sysctl vm.swapusage
```

Se o swap estiver na casa dos GB usados e a load average alta, a máquina está sem
memória — isso derruba `git`, `npm`, `curl`, até `python3` em cascata, sem relação
nenhuma com o app. A solução mais rápida costuma ser fechar aplicativos pesados ou
reiniciar a máquina, não continuar tentando o mesmo comando de novo.

## Padrão de UX para formulários (reaproveitável em qualquer app)

- **Nunca deixe um campo obrigatório sem opções travar o fluxo.** Se o usuário
  ainda não tem nenhuma "conta"/"categoria"/entidade equivalente para escolher,
  ofereça sugestões prontas de 1 toque (que já criam e selecionam) mais uma opção
  de criar personalizado — tudo **inline**, sem navegar para outra tela (navegar
  costuma perder o que já foi preenchido no formulário atual).
- **Confirme visualmente toda seleção** ("✓ Conta selecionada: Nubank") — em teste
  com usuários reais, a dúvida "isso já foi salvo?" aparece com muita frequência
  mesmo quando a interface tecnicamente já mostra o valor selecionado.
- **Campos de valor monetário**: não deixe o usuário digitar livremente um número
  cru sem formatação. Prefira um campo "mascarado" que formata como moeda a cada
  tecla digitada (dígitos acumulam da direita, tipo app de banco) — evita erros de
  vírgula/ponto e symbol da moeda mal posicionado. Ao posicionar um prefixo (cifrão)
  dentro do campo, meça a largura real do texto do prefixo via `onLayout` em vez de
  estimar por contagem de caracteres — fontes em negrito variam de largura por
  caractere e a estimativa fica sempre errada.
