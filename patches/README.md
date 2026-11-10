# Patches do Nyx2

Os patches são **opcionais**: o build funciona sem eles. Já estão commitados no branch
`nyx2-pgo`; para aplicar num clone limpo:

```powershell
cd E:\Nyx2\source
git apply ..\patches\0001-gpu-thread-pinning-4-cores.patch
git apply ..\patches\0002-default-fast-gpu-accuracy.patch
```

Reverter:

```powershell
git checkout -- src/common/core_pinning.h src/common/core_pinning.cpp src/common/settings.h src/common/thread.cpp
```

## 0001 — pinning da GPU ajustável em runtime

**Arquivos:** `src/common/core_pinning.h`, `core_pinning.cpp`, `thread.cpp`

O emulador exige 6 núcleos físicos para fixar os threads de GPU:

```
GPU thread pinning skipped: 4 physical performance cores, needs 6
```

Em vez de baixar a constante para 4 (o que exigiria recompilar para testar e para
reverter), o patch expõe `MinPerformanceCoresForPinning()`, que lê
**`EDEN_NX2_MIN_PIN_CORES`**. Sem a variável, o comportamento é **idêntico ao oficial**
(6). Com ela:

| Valor | Efeito num i7-4790 (4 físicos / 8 lógicos) |
|---|---|
| `4` | front da GPU no físico 1, back no físico 2, guest nos físicos 0 e 3 (+ irmãos SMT) |
| `3` | mesmo esquema, mas o guest fica com um único núcleo físico — agressivo |
| ausente | oficial: nada é fixado |

Usar pelo launcher:

```powershell
.\tools\Nyx2-Launch.ps1 -MinPinCores 4 -FpsLog
```

O log passa a dizer `GPU thread pinning: front and back on a physical core each, guest
cores on the other 2 performance cores (4 logical processors)` quando funciona.

Prós: tira os dois threads mais críticos da migração do Windows e da disputa de SMT.
Contras: sobram 2 núcleos físicos para as threads do guest. Por isso é **A/B**: rodar a
mesma área com e sem `-MinPinCores 4`, 60–120 s cada, e comparar com
`tools\Measure-Nyx2.ps1`. Se o guest piorar, é só não passar o parâmetro.

## 0002 — GPU accuracy Fast por padrão

**Arquivo:** `src/common/settings.h`
**Mudança:** o padrão desktop de `gpu_accuracy` passa de `High` para `Low`.

O autor mede ~12 → ~16 fps só com esse ajuste em título Switch 2 (NX2_NOTES linha 98).
A configuração do usuário já está em `0` (Low), então o patch é garantia para quando a
config for recriada, migrada ou sobrescrita por um perfil de jogo.

## Ideias avaliadas e não incluídas

| Ideia | Motivo |
|---|---|
| Fixar `MinCoresForGpuThreadPinning = 4` no código | Vira um build só para cada lado do A/B; a variável de ambiente resolve sem recompilar. |
| Mudar `EDEN_NX2_TRANSCODE_THREADS` | O padrão já é `hardware_concurrency()/4` = 2 neste PC (8 threads). |
| Desligar read traps | Piora a compatibilidade (colisores animados) e o padrão já é o melhor. |
| Forçar `EDEN_NX2_FENCE_FLUSH_US` no código | É dependente de carga; melhor testar por variável de ambiente no launcher. |
| Reduzir o layout de 12 GiB | O jogo Switch 2 exige; não é seguro. |
| Large pages para a memória do guest | Exige privilégio `SeLockMemoryPrivilege` e mexe no alocador; risco alto para ganho incerto. |
| `-ffast-math`, `-O3` no lugar do `-O2` | Risco de erro numérico em emulação de GPU; o CI usa `Release` e o PGO já otimiza os caminhos quentes. |
