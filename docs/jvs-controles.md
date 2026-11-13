# Controles del mueble (JVS): valores medidos y cómo leerlos

Valores medidos en el mueble real el **2026-11-02**, leídos con el driver MIE de KallistiOS. Úsalos en cualquier port o
parche para que los controles respondan igual en todos los juegos.

## Valores

| Control | Lectura JVS | Valores medidos |
|---|---|---|
| Volante | Analógico 0 | izquierda **8448** · centro **33792** · derecha **60160** |
| Acelerador | Analógico 1 | suelto **11776** · a fondo **47104** |
| Freno | Analógico 2 | suelto **14080** · a fondo **47360** |
| Palanca de cambios | Jugador 1 arriba / abajo | 2 posiciones con muelle (vuelve al centro): adelante = arriba, atrás = abajo |
| START | Jugador 1 START | Botón con luz (salida JVS) |
| Monedero | Contador de monedas 1 | Léelo por **diferencia de contadores** (`coin[i].count`): el pulso de moneda del driver llega ya borrado |
| TEST / SERVICE | Panel de servicio | |

El volante no es simétrico: del centro (33792) a la izquierda hay 25344 cuentas y a la derecha 26368.

## Cómo usarlos

- **Normaliza con el centro, no con el punto medio del rango:** `w = (valor - 33792)`, dividido por 25344 (si w < 0) o por
  26368 (si w > 0). Con eso el volante da -1…+1 con el cero en su sitio.
- **Margen en los pedales: ~6 % en cada extremo.** Un pedal que no llega a 0 o a 1 hace que el coche no acelere del todo
  o no frene del todo.
- **Amplía la calibración en caliente** si un control pasa de los valores medidos (los ports de este proyecto lo hacen),
  porque un mueble distinto o con otro desgaste da otros extremos.
- **Moneda:** compara el contador con el de la lectura anterior; no esperes ver el pulso.
- **El acelerador y el freno son analógicos:** si un juego los quiere como botones, pon un umbral (en los ports de este
  proyecto, a partir de un 50 %).

## Programa de prueba y calibración

`naomi/jvstest/` en [dreamcast-cannonball, rama `naomi`](https://github.com/0skarzer0/dreamcast-cannonball/tree/naomi/naomi/jvstest)
muestra los valores en bruto; en la Raspberry del mueble está cargado como `h-jvstest`.

## Implementaciones que usan estos valores

- `src/main/sdl2/input.cpp` (`poll_naomi`) en [dreamcast-cannonball](https://github.com/0skarzer0/dreamcast-cannonball/tree/naomi).
- `src/naomi/naomi_input.cpp` en [mame4all-naomi](https://github.com/0skarzer0/mame4all-naomi): además del volante
  analógico, un volante «giratorio» (spinner/dial) cuya velocidad depende del ángulo, para Super Sprint y similares.

## Combinaciones de botones en uso (no es un estándar todavía)

| Proyecto | START | Otras |
|---|---|---|
| mame4all-naomi | Pulsar = meter moneda; soltar = START | START 3 s = contador de fps |
| OutRunners | START = moneda | |
| Cannonball | START mantenido + palanca atrás = cambiar de vista; START actúa al soltarlo | Freno + START = menú principal |

Hay una [propuesta de estándar](https://github.com/0skarzer0/naomi-kit/blob/main/docs/controls.md) en naomi-kit.
Un caso real que condiciona el diseño: la hija del propietario juega a OutRun con el acelerador siempre pisado, así que
«acelerador + botón» no vale para cambiar de vista; por eso se usa START + palanca.

## Consecuencias para los juegos

- **Volante absoluto** → perfecto para juegos con volante o potenciómetro. Para spinner o dial (rotación infinita) hay
  que convertir la posición del volante en velocidad de giro (lo hace mame4all-naomi en Super Sprint).
- **Mucho recorrido para un juego de consola:** en Mario Kart 64 se multiplicó el giro por 2,5 alrededor del centro
  (la dirección a tope al 40 % del recorrido) porque había que girar demasiado (dc2naomi, perfil de mandos `mk64`).
- **Palanca de 2 posiciones con retorno** → se usa como cambio LOW/HIGH con enclavamiento por software
  (adelante = HIGH, atrás = LOW), o como dos botones (Mario Kart 64: adelante = salto/derrape, atrás = objeto). Los
  juegos con H de 4/6 velocidades quedan peor.
- **Solo un jugador** con controles de coche; un segundo jugador no tiene mandos.

## Lo que no se ha medido

- Las **luces** del botón START y de otros botones (se sabe que START tiene luz, no se ha probado a encenderla desde un
  port).
- El **volante con fuerza (force feedback)** del mueble: ningún port lo usa.
- El **panel TEST/SERVICE** más allá de leer sus interruptores.
