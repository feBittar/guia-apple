# Guia Apple

Uma skill para o Claude que revisa o seu app pelo guia oficial de design da Apple, o [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines), e arruma o que tem regra clara.

Ela começa pelo que se sente na mão. A tampa do AirPods nunca fica meio aberta: os ímãs decidem por ela. A Apple faz o mesmo na tela do iPhone, com a Central de Notificações, as folhas que sobem de baixo e o mini player do Música. Tudo que abre e fecha tem lugares certos para parar, segue o dedo, termina numa mola e dá para pegar de novo no meio do caminho. Um app feito com IA quase nunca faz isso.

Depois ela confere o que dá para medir: área de toque, tamanho e contraste do texto, modo escuro, área segura, carregamento, Reduzir Movimento e rótulos para o leitor de tela.

Ela abre as páginas do guia na hora e cita o link de cada regra que usou.

**Um exemplo do teste.** Um site de receitas com uma folha de filtros que sobe de baixo. Antes:

> a folha parava onde o dedo soltava, andava 50 ms atrás do dedo (um `setTimeout` no arrasto) e abria e fechava numa animação linear de meio segundo que não dava para interromper

Depois de rodar `/guia-apple`:

| onde | o que o guia pede | antes | depois |
|---|---|---|---|
| folha de filtros | lugares certos para parar ([Sheets](https://developer.apple.com/design/human-interface-guidelines/sheets)) | parava onde o dedo soltava | abre ou fecha pela posição e pela velocidade, numa mola. Um peteleco curto para baixo fecha |
| folha, durante o arrasto | movimento que segue o gesto ([Motion](https://developer.apple.com/design/human-interface-guidelines/motion)) | andava 50 ms atrás do dedo | anda junto com o dedo |
| ícones ♡ e ↗ | área de toque de pelo menos 44 ([Buttons](https://developer.apple.com/design/human-interface-guidelines/buttons)) | 28, com 4 px entre eles | o desenho ficou igual, o toque pega 44 |
| "45 min · fácil" | contraste de 4,5:1 ([Accessibility](https://developer.apple.com/design/human-interface-guidelines/accessibility)) | 2,05:1 | o mesmo cinza mais escuro, 4,54:1 |

Ela abriu o site num navegador e arrastou de verdade: em 40 arrastos aleatórios a folha terminou aberta ou fechada em todos. O tamanho do texto da página inteira e o modo escuro ela deixou como pergunta, porque mexem no app todo.

## Instalar

### No Claude Code

Cole esta frase na conversa:

```text
Claude, instala a skill que tá nesse repositório pra eu usar em qualquer projeto e confere antes se tá tudo seguro: https://github.com/feBittar/guia-apple
```

O Claude lê o `SKILL.md`, diz o que ele faz e copia o arquivo para `~/.claude/skills/guia-apple/` (no Windows, a pasta `.claude\skills\guia-apple\` dentro da sua pasta de usuário). No modo padrão do Claude Code, ele pede a sua permissão antes de baixar e antes de gravar. Se o `/guia-apple` não aparecer depois, feche e abra o Claude Code. Para instalar à mão, copie o `SKILL.md` para essa pasta. Para desinstalar, apague essa pasta.

A skill é um arquivo de texto só, sem programa junto.

### No claude.ai

Baixe o [guia-apple.zip](https://github.com/feBittar/guia-apple/releases/latest/download/guia-apple.zip), vá em Customize > Skills, clique no "+" e em "Upload a skill", e envie o zip. Se a opção não aparecer, ligue a execução de código em Settings > Capabilities.

No navegador o Claude não abre a pasta do seu projeto. Cole o código da tela na conversa e ele devolve o trecho corrigido.

### No ChatGPT, no Gemini e em outras IAs

Copie o conteúdo do `SKILL.md` e cole como primeira mensagem de uma conversa nova, junto com o código da tela que você quer revisar.

## Usar

Abra o Claude Code na pasta do seu app e chame a skill:

```text
/guia-apple
```

Para olhar um arquivo ou uma pasta só:

```text
/guia-apple src/components/BottomSheet.tsx
```

Ou peça do seu jeito:

```text
revisa meu app com o guia de design da Apple
```

Para só conferir, sem mudar nenhum arquivo:

```text
/guia-apple só olha, não muda nada
```

## O que ela devolve

1. Uma frase com o resultado: quantas coisas olhou, quantas corrigiu e quantas deixou para você decidir.
2. Uma tabela com onde, o que o guia pede (com o link), o antes e o depois.
3. O que ela viu e deixou, com o motivo.
4. O que precisa de você: o que mexe na sua marca, no tema ou na navegação vem como sugestão.
5. Como ela conferiu e quais páginas do guia abriu.

## A regra da tampa

Tudo que se arrasta para abrir ou fechar (uma folha, uma gaveta, um menu lateral, um mini player):

1. tem lugares de descanso, que o guia chama de *detents*, e nunca para fora deles;
2. segue o dedo um para um durante o arrasto;
3. ao soltar, decide pela posição e pela velocidade do dedo, com a conta de projeção que a Apple mostrou na WWDC18;
4. chega lá numa mola, sem quique por padrão;
5. dá para pegar de novo no meio da animação;
6. estica quando passa do limite;
7. no iPhone, dá um toque leve quando encaixa.

Ela escreve isso do jeito de cada plataforma: em SwiftUI e UIKit usa o que o sistema já tem (folha com `presentationDetents`, `predictedEndTranslation`, molas); em site, React Native, Flutter e Compose faz a conta e usa uma mola.

## Limites

- A skill foi testada em três casos pequenos: um site, aberto e testado num navegador, um app em React Native e um em SwiftUI. Os dois apps não foram rodados nem compilados nos testes, feitos sem Xcode e sem as dependências instaladas.
- No SwiftUI, uma peça que a pessoa pega no meio da mola pula para onde o estado diz: o SwiftUI não mostra ao código onde ela está. A skill avisa e oferece o caminho do UIKit quando isso importa.
- Ela não redesenha o seu app. Cor da marca, fonte e layout ficam; o que mexe nisso vem como sugestão. E não troca um valor que o app inteiro usa sem perguntar.
- O guia é da Apple. Em app Android, ela segue o que é do Android (área de toque de 48 dp, botão voltar do sistema). Em site, usa o guia como referência.
- Ela aplica a correção sem perguntar quando a regra é clara. Para ver só o relatório antes, use `/guia-apple só olha, não muda nada`. E guarde o seu trabalho no git antes de rodar num app inteiro, para poder voltar atrás.

## Fontes

- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines), da Apple: as páginas Motion, Gestures, Sheets, Buttons, Accessibility, Typography, Color, Dark Mode, Layout, Loading, Feedback e Playing haptics.
- [Designing Fluid Interfaces](https://developer.apple.com/videos/play/wwdc2018/803/), sessão 803 da WWDC18: a projeção pela velocidade, as molas sem quique e o movimento que segue o dedo.
- [Animation](https://developer.apple.com/documentation/swiftui/animation), na documentação do SwiftUI.

A skill não copia o texto do guia: ela manda o Claude ler as páginas oficiais na hora e citar cada uma.

## Licença

MIT. Veja o arquivo [LICENSE](LICENSE). A skill é do Felipe, [@ofelipemart](https://www.instagram.com/ofelipemart/) no Instagram.
