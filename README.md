# PeerLive

**Transmita sua tela para os amigos do canal de voz e assistam uns aos outros dentro do Discord, direto de PC para PC, de graça e sem servidor nosso no meio.**

> **Status: em teste.** O PeerLive ainda não é uma versão final. Já funciona em testes reais, mas há partes em andamento (veja [Status](#status)). Quer testar? Vá para [Como instalar a versão de teste](#como-instalar-a-versão-de-teste).

---

## O que é

O PeerLive é um plugin para o [Vencord](https://github.com/Vendicated/Vencord) (um mod do cliente do Discord) que permite que quem está no **mesmo canal de voz** transmita a **tela inteira ou uma janela** para os outros e todos assistam **dentro do Discord**.

- **Peer-to-peer (P2P):** o vídeo vai direto de um PC para o outro, via WebRTC.
- **Gratuito e sem servidor nosso:** não existe backend do PeerLive. Ninguém paga nada e nada passa por nós.
- **Até 1080p a 60 fps.** (A captura no Windows moderno é limitada a cerca de 60 fps.)
- **Som da janela:** ao transmitir uma janela, o som daquele aplicativo vai junto (só dele, não do resto do PC).
- **Som da tela inteira:** ao transmitir a tela inteira, vai todo o som do PC, **menos o do Discord** (seus amigos não ouvem a própria voz de volta). Exige Windows 10 versão 2004 ou superior.
- **Codec automático:** H.264 por hardware quando os dois lados suportam; caso contrário, VP9.

## Como funciona

Os amigos precisam se achar uns aos outros e, depois disso, o vídeo segue por conexão direta.

```
                 relays públicos do Nostr
                (via biblioteca Trystero)
                 só para "se encontrar"
                    ^                ^
                    |                |
              "estou aqui"     "estou aqui"
                    |                |
   +----------------+--+          +--+----------------+
   |  PC de quem       |  vídeo   |  PC de quem       |
   |  transmite        | =======> |  assiste          |
   |  Discord+PeerLive | (WebRTC, |  Discord+PeerLive |
   +-------------------+  direto) +-------------------+
```

1. Cada PC entra numa "sala" derivada do canal de voz do Discord e avisa que está lá. Esse aviso passa por **relays públicos do Nostr** (usando a biblioteca [Trystero](https://github.com/dmotz/trystero)), e **só serve para descobrir quem está por perto**.
2. Os PCs fazem o handshake WebRTC e abrem uma **conexão direta**. Usamos apenas servidores **STUN** (para descobrir endereço público); **o vídeo e o áudio nunca passam por servidor**.
3. Quem transmite captura a tela ou a janela e envia para cada espectador.

## Status

Em teste. O que já foi **verificado**:

- [x] Captura de tela e de janela dentro do Discord oficial, no Windows.
- [x] Áudio da janela.
- [x] Conexão direta entre duas casas diferentes (720p, ~57 fps, 0% de perda).
- [x] Assistir em uma janela separada do Discord.

Em andamento / planejado:

- [ ] **Em andamento:** assistir dentro da própria tela da call (hoje a transmissão abre numa janela própria).
- [ ] **Disponível, ainda em validação:** áudio ao transmitir a **tela inteira** (todo o som do PC, menos o do Discord; veja as [Limitações](#limitações)).
- [ ] **Planejado:** distribuição final usando o **Vencord oficial** + um instalador próprio do PeerLive, sem precisar de um build modificado (veja o aviso abaixo).

## Requisitos

- Windows 10 (versão 2004 ou superior) ou Windows 11.
- Discord desktop com o **Vencord oficial já instalado** ([vencord.dev/download](https://vencord.dev/download/)).
- **Todo mundo que transmite ou assiste precisa ter o PeerLive.**
- Funciona com a aceleração de hardware do Discord **ligada ou desligada** (desligada usa mais CPU).

## Como instalar a versão de teste

> **Aviso:** a versão de teste atual é um **build de teste**: um fork do Vencord 1.15.13 com o plugin PeerLive incluído. Ele **substitui os arquivos** da pasta `%APPDATA%\Vencord\dist` (guardando uma cópia dos arquivos oficiais antes). Suas configurações, temas e outros plugins não são tocados. No modelo final planejado, você manterá o Vencord oficial e adicionará o PeerLive com um instalador próprio.

1. Instale o **Vencord oficial** em [vencord.dev/download](https://vencord.dev/download/), caso ainda não tenha. (Sem ele, o instalador avisa e não faz nada.)
2. Baixe o ZIP `PeerLive-teste-….zip` na página de [**Releases**](https://github.com/HumbertoFernandes7/Telinha/releases). As versões de teste são marcadas como **pre-release**.
3. Se o Windows reclamar (SmartScreen/antivírus), **antes de extrair**: botão direito no ZIP > Propriedades > marque **Desbloquear** > OK. Os scripts são texto puro, e você pode abri-los no Bloco de Notas para conferir.
4. Botão direito no ZIP > **Extrair tudo**. (Não rode de dentro do ZIP.)
5. Dê dois cliques em **`instalar.cmd`** e espere aparecer "Instalação concluída".
6. **Feche o Discord por completo**: botão direito no ícone dele na bandeja do sistema (perto do relógio) > "Sair do Discord". Abra de novo.
7. Vá em **Configurações do usuário > Vencord > Plugins**, pesquise **PeerLive** e **ative** o plugin.

**Para desfazer tudo**: feche o Discord por completo, dê dois cliques em **`restaurar.cmd`** e abra o Discord de novo. Isso volta ao Vencord oficial.

## Como usar

**Transmitir**

1. Entre no mesmo canal de voz que os amigos (todos com o PeerLive).
2. No painel da conexão de voz (canto inferior esquerdo), clique em **"Transmitir com PeerLive"**.
3. Escolha uma **tela** ou uma **janela**. Janela = vai com o som daquela janela; tela inteira = vai todo o som do PC, menos o do Discord.
4. Escolha a qualidade (até 1080p60) e clique em **Transmitir**.

**Assistir** (qualquer uma destas formas)

- Clique na etiqueta **PEERLIVE** ao lado do nome da pessoa na lista do canal de voz.
- Clique em **Assistir** na barra do PeerLive.
- Menu da caixa de ferramentas do Vencord (topo do Discord) > **Quem está transmitindo**.

Por enquanto, a transmissão abre em uma janela própria, com tela cheia, volume e "Parar de assistir".

**Jogos:** use o modo **borderless / sem bordas**. Em tela cheia exclusiva a imagem pode aparecer preta.

## Privacidade

- **Conexão direta significa que quem está na call pode ver o IP das outras pessoas.** Use só com quem você confia.
- Por padrão, **só são aceitas pessoas que estão no mesmo canal de voz** (configuração "Rejeitar quem não está na call").
- Opcional: nas configurações do plugin você pode definir uma **senha do grupo** (todos precisam usar a mesma).
- Os relays do Nostr só servem para as pessoas se encontrarem; eles não recebem seu vídeo.
- Os **logs do plugin nunca contêm endereços IP**.
- Não existe servidor nosso que guarde dados: o PeerLive não tem backend.

## Limitações

- **Cada espectador recebe uma cópia separada** do vídeo, enviada pelo PC de quem transmite. O upload cresce com o número de espectadores; recomendamos **2 a 4 espectadores**. Em 1080p60, 1 ou 2 é o ideal.
- Sem aceleração de hardware no Discord, o PeerLive usa mais CPU.
- Jogos em **tela cheia exclusiva** podem aparecer pretos. Use borderless.
- Como só usamos STUN (sem servidor de retransmissão), em redes muito restritivas a conexão direta pode não se estabelecer.
- O PeerLive **não injeta DLLs em jogos** (de propósito, por segurança com sistemas anti-cheat).
- O áudio da tela inteira usa um pequeno programa auxiliar que vem no pacote, o `peerlive-audio.exe` (Windows 10 versão 2004 ou superior). Ele **ainda não é assinado digitalmente**, então o antivírus ou o Controle Inteligente de Aplicativos (Smart App Control) do Windows pode marcá-lo ou bloqueá-lo. Se for bloqueado, a tela é transmitida **sem som** (o seletor mostra o motivo); transmitir uma janela com som não depende dele.
- A visualização dentro da tela da call ainda está em desenvolvimento.

## Perguntas frequentes

**Por que cada pessoa precisa ter o PeerLive?**
Porque o vídeo não passa pelo Discord nem por nenhum servidor: ele vai direto entre os PCs, e é o plugin que faz a captura, a conexão e a exibição de cada lado. Sem o PeerLive em quem assiste, não há como receber a transmissão.

**Funciona sem aceleração de hardware?**
Sim. Funciona com a aceleração de hardware do Discord ligada ou desligada. Desligada, usa mais CPU. O codec é escolhido automaticamente: H.264 por hardware quando os dois lados têm, senão VP9.

**Dá ban?**
Mods de cliente **violam os Termos de Serviço do Discord**, então o uso é **por sua conta e risco**. Não temos conhecimento de bans por plugins que não fazem nada abusivo, mas não há garantia nenhuma.

**Isso é afiliado ao Discord ou ao Vencord?**
Não. O PeerLive não é afiliado ao Discord nem à equipe do Vencord, e a equipe do Vencord **não dá suporte** a este build. Não peça suporte do PeerLive por lá.

**Preciso pagar ou criar conta em algum serviço?**
Não. É gratuito e não existe servidor nem conta do PeerLive.

**Qualquer pessoa pode entrar na minha transmissão?**
Por padrão, só quem está no mesmo canal de voz que você (e tem o PeerLive). Você também pode definir uma senha do grupo.

**Posso transmitir meu jogo?**
Pode, de preferência em modo borderless (tela cheia exclusiva pode aparecer preta). O PeerLive não injeta DLL nenhuma no jogo, de propósito, por segurança com anti-cheat.

**O instalador vai estragar meu Vencord?**
Ele guarda uma cópia dos arquivos oficiais (`%APPDATA%\Vencord\dist.backup-oficial-…`) e o `restaurar.cmd` volta tudo ao original. Não apague essa pasta de backup até terminar os testes. Se o Discord não abrir, rode o `restaurar.cmd`; se nem isso resolver, reinstale o Vencord oficial em [vencord.dev/download](https://vencord.dev/download/).

**Deu problema. E agora?**
Nas configurações do plugin, clique em **"Copiar diagnóstico"** e mande o texto para quem compartilhou o PeerLive com você ou para o autor (Humberto, [@HumbertoFernandes7](https://github.com/HumbertoFernandes7)). Os logs não contêm IPs.

## Para desenvolvedores

Requisitos: Node.js e [pnpm](https://pnpm.io/).

```sh
pnpm install
pnpm build --standalone --disable-updater   # build do Vencord com o PeerLive
pnpm smoke:renderer                         # smoke test de carregamento do renderer
pnpm package:test                           # gera o ZIP do pacote de teste
```

- O código do plugin fica em [`src/plugins/peerLive/`](src/plugins/peerLive/). O protocolo P2P está em `src/plugins/peerLive/protocol.ts`.
- **Amigo virtual** para testar sozinho, sem uma segunda pessoa: uma página que fala o mesmo protocolo do plugin, em [`tools/peerlive-peer/`](tools/peerlive-peer/). Sirva com:

  ```sh
  python -m http.server 8788 --directory tools/peerlive-peer
  ```

  e abra `http://localhost:8788`. Detalhes em [`tools/peerlive-peer/README.md`](tools/peerlive-peer/README.md).
- As constantes do protocolo estão duplicadas entre o plugin e o amigo virtual; mantenha as duas em sincronia.

## Créditos e licença

Criado por **Humberto Fernandes** ([@HumbertoFernandes7](https://github.com/HumbertoFernandes7)).

O PeerLive é baseado no [**Vencord**](https://github.com/Vendicated/Vencord), da equipe do Vencord, e usa a biblioteca [Trystero](https://github.com/dmotz/trystero) para descoberta de pares. Este projeto **não é afiliado** ao Discord nem à equipe do Vencord.

Licenciado sob a **GPL-3.0-or-later**, a mesma licença do Vencord. Veja o arquivo [`LICENSE`](LICENSE).

---

## English summary

**PeerLive** lets friends in the same Discord voice channel stream their screen or a window to each other and watch **inside Discord**, directly between their PCs (peer-to-peer, WebRTC). It is free and there is no server of ours: public Nostr relays (through the Trystero library) are used only so peers can find each other, and the video goes straight PC to PC (STUN only). It supports up to 1080p at 60 fps, and captures audio: the sound of a single window, or, for whole-screen streams, all PC sound except Discord's (Windows 10 2004+; done by a small unsigned helper, `peerlive-audio.exe`, which antivirus or Smart App Control may block, in which case the screen streams without sound).

It is a plugin for [Vencord](https://github.com/Vendicated/Vencord) (a Discord client mod), and it is currently **in testing**. The test release is a **fork of Vencord 1.15.13 with the PeerLive plugin**, installed with `instalar.cmd` from the zip in [Releases](https://github.com/HumbertoFernandes7/Telinha/releases) (pre-release); `restaurar.cmd` reverts it. It needs official Vencord to be installed first. The planned final model is to keep the official Vencord and add PeerLive with its own installer.

- **Requirements:** Windows 10 2004+/11, Discord desktop with official Vencord. Everyone who streams or watches needs PeerLive.
- **Verified so far:** screen/window capture in stock Discord on Windows, window audio, a direct connection between two homes (720p, ~57 fps, 0% loss), viewing in a separate Discord window. Watching inside the call view is in progress.
- **Privacy:** a direct connection means people in the call can see each other's IP. Only people in the same voice channel are accepted by default, there is an optional group password, and logs never contain IP addresses.
- **Limitations:** each viewer gets a separate copy from the streamer (2-4 viewers recommended); exclusive-fullscreen games may show black (use borderless); no DLL injection into games.
- **Disclaimer:** client mods violate Discord's Terms of Service, so use it at your own risk. This project is not affiliated with Discord or the Vencord team, and the Vencord team does not support this build.
- **License:** GPL-3.0-or-later, like Vencord.
