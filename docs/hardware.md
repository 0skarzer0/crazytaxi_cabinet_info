# El mueble: placa, NetDIMM, netbooter y monitor

Medido y usado en el mueble de este proyecto (Crazy Taxi de SEGA). Fechas en cada dato cuando se conocen.

## Resumen

| Elemento | Detalle |
|---|---|
| Placa | **NAOMI 2** con multibios. La BIOS de exportación (EPR-23608B) **no se sube a ningún repositorio** |
| Memoria de juego | **NetDIMM de 512 MB**, firmware **3.17** |
| Carga | **Netboot** desde una **Raspberry Pi 3** con **WiPi Netbooter**: ficheros `.bin.gz` + una fila en `romsinfo.csv` |
| Red | La Raspberry está en la red de casa y llega a la NetDIMM por una red propia (en este mueble, la NetDIMM es `10.0.0.4` para la Raspberry). **La NetDIMM solo se ve desde la Raspberry** |
| Monitor | **31 kHz, 640x480** (no trifrecuencia) |
| Controles | JVS: volante, acelerador, freno, palanca de dos posiciones, START con luz, monedero, panel TEST/SERVICE ([jvs-controles.md](jvs-controles.md)) |

## Cómo se carga un juego

1. La imagen es un `.bin.gz` (**gzip obligatorio**: WiPi lee los últimos 4 bytes del fichero como tamaño original).
2. Se copia a `/boot/roms/` de la Raspberry y se añade su fila a `/boot/config/romsinfo.csv`, que se importa a la web
   del netbooter.
3. Se elige en el menú y la Raspberry lo envía a la NetDIMM; la placa arranca sola.
4. Mientras el juego corre, `timehack.py` (de WiPi) tiene que seguir vivo en la Raspberry: si no, la placa se reinicia a
   los pocos minutos con «error 33 gateway is not found».

Los scripts y las trampas de este proceso: [naomi-pi-tools](https://github.com/0skarzer0/naomi-pi-tools).

> **Una imagen netboot contiene las ROMs y las grabaciones del usuario: no se publica.** Cada persona genera las suyas
> con sus propios volcados.

## Sobre el vídeo

- La salida es **640x480 a 31 kHz**. Los juegos que van a otra resolución (p. ej. Super Sprint, 512x384) se centran o se
  amplían dibujando una imagen más grande, no estirando la señal: según la documentación general de la Dreamcast, el
  escalador de salida de la PowerVR2 está pensado para reducir y en horizontal solo duplica píxeles, así que no sirve
  para pasar de 512 a 640. 🧪 No se ha probado en esta placa; lo que sí se midió es el método que se usa (ver
  [trampas-hardware-real.md](trampas-hardware-real.md), «Ampliar la imagen»).
- La NAOMI 2 lleva dos PowerVR2 y el chip de geometría Elan, pero hasta donde sabemos KallistiOS trata la placa como
  una NAOMI 1 y las medidas de este proyecto son todas de ese modo. El cuello de botella de los ports ha sido siempre la
  CPU (SH-4), no el chip gráfico.

## Otro mueble en la misma casa

El propietario tiene además un mueble NAOMI 2 estándar con otra Raspberry (otra NetDIMM). **No se ha medido**: los valores
de este repositorio son del Crazy Taxi y no tienen por qué valerle.
