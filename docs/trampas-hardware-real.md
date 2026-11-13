# Trampas del hardware real (NAOMI 2 + NetDIMM)

Cosas que funcionan en un emulador (Flycast) y fallan, o van mucho peor, en la placa real. Cada punto viene de una
medida o un fallo observado en este mueble.

## Rendimiento de CPU

- Los núcleos de emulación van entre **2 y 3,5 veces más lentos** en la SH-4 de la placa que en Flycast sobre PC
  (medido: FAMEC 3,3×, RAZE Z80 2,3×, Musashi 1,8×). No calcules el presupuesto de un port con el emulador.
- **Caché de instrucciones de 8 KB:** el código recompilado estáticamente (T11 de Super Sprint) engorda y compite por
  la caché; en la placa ganó ~8 % frente a ~35 % en Flycast. `-Os` salió peor que `-O2`. Mide siempre en la placa.
- Un dato que despista: el intérprete lento + el contador de fps del emulador dan cifras distintas de las del juego;
  mide con el bloque de telemetría (ver naomi-pi-tools).

## Vídeo / PVR

- **La VRAM no admite escrituras de byte.** Escribe en palabras de 16 o 32 bits.
- **El DMA del PVR se cuelga en NAOMI.** Copia por CPU o con store queues.
- **Buffer de vértices:** con 64 KB el juego cayó a ~5 fps; sube a 512 KB.
- **Texturas de 8 bpp:** tienen que estar en orden *twiddled*, con paleta de 1024 entradas y lista punch-through (PT)
  si hay transparencia de color clave.
- **Ampliar la imagen:** el filtro bilinear por pieza rompe los trazos finos de los textos en listas PT y deja costuras.
  Lo que funcionó: dibujar 1:1 sin filtro en una textura 512x384 RGB565 (render a textura, `pvr_scene_begin_rtt`) y
  mostrarla a 640x480 con bilinear. Las alternativas con varias pasadas (sharp bilinear x2) bajaron a 25-30 fps.
- **Render a textura:** KOS solo lanza el render al cerrar la última lista si el PVR está libre; si no, espera al
  siguiente vblank. Llama a `pvr_wait_render_done()` antes de cerrar la última lista de la escena a textura, pero **no**
  antes de la escena de pantalla (eso bajó de 60 a 35-41 fps).
- **Escribir al VRAM directamente para un overlay** (contador de fps) llegó a colgar la placa; dibuja el overlay con el PVR.
- La salida es solo **640x480**.

## Sonido

- Subir la música de 11 a 22 kHz cuesta ~2 ms por fotograma y bajó de 60 a 53-54 fps en Super Sprint. Se dejó en 11 kHz.

## NetDIMM y carga

- WiPi Netbooter solo carga **`.bin.gz`**; hace falta la fila en `romsinfo.csv` (¡con salto de línea final!, si falta,
  la fila se pega a la anterior).
- **`timehack.py` debe seguir vivo:** si se para, la placa se reinicia con «error 33 gateway is not found».
- Para pararlo: `pkill -f "[t]imehack.py"` (con corchetes; sin ellos `pkill -f` se mata a sí mismo).
- **Peek (telemetría):** sobre programas que no responden a las peticiones devuelve basura durante 17-30 s.
  La dirección del bloque en vivo cambia con cada compilación; se lee del mapa del enlazador.
- Nunca pongas contraseñas en ficheros del repositorio (las del Pi se dieron solo en el chat).

## Controles

- **Flycast:** emula pedales con teclas en modo todo/nada (variable `FC_PEDALS`), así que un juego puede parecer correcto
  allí y fallar con los analógicos reales del mueble (margen del 6 %; ver [jvs-controles.md](jvs-controles.md)).
- El pulso de moneda del driver JVS llega ya borrado: usa el contador.
