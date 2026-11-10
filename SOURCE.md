# Código-fonte correspondente

O binário distribuído em **Releases** é GPL-3.0-or-later. Para reproduzir a mesma build:

## 1. Upstream

```
https://github.com/zortos293/Lichii
```

Base usada: commit `0ab7736df9dba2b396d2c7a26bd26fbb9b7b0dc0`
(*video_core: shader cache v2 as the Vulkan pipeline cache*).

## 2. Alterações do Nyx2

- `source-patch/nyx2-v1.0-source.patch` — diff completo dos arquivos rastreados contra o commit
  acima (rebranding, versão, ícone, auto-update desligado, relatório de compatibilidade
  desligado, cache `nyx2-cache.v2.bin`, etc.).
- `patches/0001-*.patch` e `patches/0002-*.patch` — os dois ajustes de desempenho, também
  incluídos no diff acima (ficam aqui separados para leitura).
- `branding/nyx2.ico`, `branding/nyx2.png` — ícone do aplicativo (novos, não estavam no upstream).

Aplicando:

```bash
git clone https://github.com/zortos293/Lichii
cd Lichii
git checkout 0ab7736df9dba2b396d2c7a26bd26fbb9b7b0dc0
git apply /caminho/nyx2-v1.0-source.patch
cp /caminho/nyx2.ico dist/nyx2.ico
```

## 3. Build

- CMake + Ninja, MSVC/`clang-cl` 22.1.8
- Release com **ThinLTO + PGO** e `-march=haswell`
- Scripts de build originais estão em `tools/` do upstream; o perfil PGO de treino
  (`nyx2-windows.profdata`, ~19 MB) não vai no repositório por tamanho, mas pode ser gerado ou
  solicitado.

## 4. Sem o que não é código

Não fazem parte deste repositório: chaves do console, firmware, jogos, arquivos de configuração,
saves e caches gerados em tempo de execução.

Se você recebeu o binário e precisa de algum arquivo do fonte que não esteja aqui, abra uma issue.
