# crazytaxi_cabinet_info — el mueble Crazy Taxi (NAOMI 2 + NetDIMM): hardware, controles JVS y trampas del hardware real

Todo lo que se ha **medido** en un mueble **Crazy Taxi de SEGA** (NAOMI 2, volante, acelerador, freno, palanca de dos
posiciones, START con luz, monedero) al llevarle juegos de coches, motos y spinner por netboot. Está pensado para quien
tenga **otro Crazy Taxi** o un mueble parecido y quiera reutilizar los valores, o para quien escriba un port a NAOMI y
no quiera tropezar con lo que ya nos ha costado días.

*In English:* measurements and pitfalls of a SEGA Crazy Taxi upright (NAOMI 2, 512 MB NetDIMM, netbooted from a
Raspberry Pi): JVS values of the wheel and pedals, how the cabinet is wired to a netboot setup, and what the real
hardware does differently from Flycast (CPU cores 2-3.5x slower, no byte writes to VRAM, PVR quirks). Documents in
Spanish, code names in English. No ROMs, no BIOS.

> **Sin ROMs ni BIOS.** Aquí solo hay medidas y documentación. La BIOS del mueble no se sube (ni se enlaza).

## Documentos

| Documento | Qué hay |
|---|---|
| [docs/hardware.md](docs/hardware.md) | La placa, la NetDIMM, el netbooter y el monitor: qué es cada cosa y cómo está conectado |
| [docs/jvs-controles.md](docs/jvs-controles.md) | **Valores JVS medidos** del volante, los pedales, la palanca, START y el monedero; cómo leerlos y calibrarlos |
| [docs/trampas-hardware-real.md](docs/trampas-hardware-real.md) | Lo que la placa real hace distinto de Flycast: CPU, VRAM, PowerVR2, vídeo. Con medidas |
| [HANDOFF.md](HANDOFF.md) | Estado, qué falta por medir y siguientes pasos |

## En una frase cada cosa

- **Volante** (JVS, analógico 0): izquierda **8448** · centro **33792** · derecha **60160**.
- **Acelerador** (analógico 1): suelto **11776** · a fondo **47104**. **Freno** (analógico 2): suelto **14080** · a fondo **47360**.
- **Monedero:** se lee por *diferencia de contadores*; el pulso de moneda del driver llega ya borrado.
- **Los núcleos de CPU emulados van 2–3,5 veces más lentos en la placa real que en Flycast.** Mide siempre en la placa.
- **La VRAM real no admite escrituras de byte**, y la DMA del PowerVR2 se cuelga en NAOMI.
- **Vídeo:** monitor de 31 kHz, 640x480; no hay un modo de 512x384.

## Dónde encaja

Es la parte «el mueble» de [naomi-wheel-games](https://github.com/0skarzer0/naomi-wheel-games) (índice y estado de
cada juego). Los scripts de la Raspberry están en [naomi-pi-tools](https://github.com/0skarzer0/naomi-pi-tools). Qué
cambió SEGA entre el Crazy Taxi de Dreamcast y el de NAOMI:
[docs/crazy-taxi-dreamcast-vs-naomi.md](https://github.com/0skarzer0/naomi-wheel-games/blob/main/docs/crazy-taxi-dreamcast-vs-naomi.md).

## Agradecimientos

- **KallistiOS** y su subarquitectura NAOMI, con el driver MIE con el que se leen los controles JVS.
- **[DragonMinded](https://github.com/DragonMinded)**: libnaomi y las herramientas de netboot, base de todo lo que se
  mide y se envía a la placa.
- **WiPi Netbooter** (chunksin y colaboradores) y **Piforce**: el netbooter de la Raspberry.
- **Flycast** y sus colaboradores: el emulador con el que se prueba antes de la placa y con el que se compara.
- **Claude** (Anthropic), a través de Claude Code: las medidas y el diagnóstico se han hecho en sesiones con el
  propietario del mueble, que es quien prueba en la placa y aporta las mediciones.

Las licencias de los proyectos citados siguen siendo las suyas.
