# Nyx2 — v1.0 (build de teste)

Emulador experimental para Windows, derivado do **Lichii** (que é fork do **Eden**, que é fork do
**yuzu**). Código sob **GPL-3.0-or-later**. Não é afiliado, patrocinado nem endossado pela Nintendo.

Esta é a **primeira versão de teste** da marca Nyx2: nome, ícone, versão e ajustes de desempenho
próprios, com o motor de emulação vindo dos projetos acima.

## Download

Pegue o ZIP em **[Releases](../../releases)** → `v1.0` → `Nyx2-v1.0.zip` (47,8 MB).

```
SHA256  0c93e7b363a47a5bf192a52415c603fe902a433e5c23eceebd08ac2e82736f8c
```

## Conteúdo do pacote

- `Nyx2.exe` (emulador) + `Nyx2 Launcher.exe`, `Nyx2 Jogo.exe`, `Nyx2 Medir.exe`
- DLLs do Qt, `opengl32sw`, `dxcompiler`, `dxil`, `d3dcompiler_47`, `nxformat`
- runtime do VC++ app-local (`msvcp140*`, `vcruntime140*` — não precisa instalar redist)
- `nx2_switches.txt`, `README.txt`, `NOTICE.txt`

**Não** inclui chaves (`prod.keys`), firmware nem jogos — e não vai incluir.

## Requisitos

- Windows 10/11 64 bits
- GPU com Vulkan (testado em RTX 3060 Ti)
- CPU com AVX2: Haswell ou mais novo (o build é otimizado com `-march=haswell`)
- 16 GB de RAM recomendado

## Como usar

1. Descompacte numa pasta simples, ex.: `C:\Nyx2` (evite OneDrive / pasta sincronizada).
2. Coloque suas chaves em `%APPDATA%\eden\keys\prod.keys` (e o firmware em
   `%APPDATA%\eden\bis\system\Contents\registered`).
3. Rode `Nyx2 Launcher.exe` → gerenciador de jogos. Arraste o jogo (`.nup`, `.nsp`, `.xci`) para a
   janela ou use **File → Load**.
4. **Primeira execução:** o emulador compila os shaders (~5459 pipelines). Num PC de 4 núcleos isso
   leva ~10 minutos, com a tela "Loading Shaders X / 5459". **Não feche no meio** — o cache só é
   gravado no fim. Depois o boot fica rápido.
5. Não troque precisão de GPU, resolução, anti-aliasing ou driver sem saber: cada mudança invalida o
   cache e recompila tudo.

Se você já usa Lichii/Eden neste PC, nada precisa ser configurado: os dois usam a mesma pasta de
dados (`%APPDATA%\eden`), incluindo chaves, firmware, saves e biblioteca. O cache de shader, porém,
**não** é reaproveitado (o arquivo tem outro nome, `nyx2-cache.v2.bin`), então espere uma
recompilação no primeiro boot. Não rode Nyx2 e Lichii ao mesmo tempo.

## Antivírus

O `.exe` não tem assinatura digital e o emulador compila código em tempo real (JIT, memória
executável). Isso faz o **escudo comportamental** do Avast/AVG acusar `IDP.HELU.PSDxx`, e o
SmartScreen mostrar "O Windows protegeu o seu PC". É **falso positivo**:

- libere a pasta do Nyx2 nas exceções do seu antivírus **antes** de rodar;
- se já caiu em quarentena, escolha **Restaurar** (é o mesmo arquivo);
- na caixa "Ameaça bloqueada", escolha **Permitir / Restaurar e adicionar exceção**.

## Código-fonte

Ver [SOURCE.md](SOURCE.md). Em resumo: upstream + patches deste repositório reproduzem esta build.

## Licença

GPL-3.0-or-later — veja [LICENSE.txt](LICENSE.txt) e [NOTICE.txt](NOTICE.txt).

Sem garantias. Uso por sua conta e risco.
