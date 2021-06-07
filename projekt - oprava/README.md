# Tachometr pro jízdní kolo - oprava

Vypracovali: Jan Mostecký (221478), Klimeš Jiří (212561), Němec Petr (221480), 

[Odkaz na github složku](https://github.com/JanMostecky/Digital-electronics-1/tree/main/projekt%20-%20oprava)

[Původní verze projektu](https://github.com/JanMostecky/Digital-electronics-1/tree/main/project)

------------------------------

## Cíl Práce

Cílem práce bylo opravit původní neuznané řešení návrhu cyklo-tachometru. 

Realizovat jsme se rozhodli následující funkce tachometru: Měření rychlosti a měření ujeté vzdálenosti. 
Pro měření rychlosti byly zvoleny kilometry v hodině a pro ujetou vzdálenost kilometry. Obě jednotky budou zobrazeny čtyřmístným číslem obsahujícím desítky metrů, stovky metrů, kilometry a desítky kilometrů. 

-----------------------------

## Použitý Hardware

Vytvořený kód měl být implementovatelný na desky Arty A7 - 35T, or Arty A7 - 100T. Pro tento projek byla zvolena deska Arty A7 - 35T.

Odkaz na stránky výrobce desky: Artix-7 FPGA Development Board:
https://store.digilentinc.com/arty-a7-artix-7-fpga-development-board/

Technická dokumentace desky, uživatelské fórum: 
https://reference.digilentinc.com/reference/programmable-logic/arty-a7/start

Manuál:
https://reference.digilentinc.com/reference/programmable-logic/arty-a7/reference-manual

Schéma:
https://reference.digilentinc.com/_media/reference/programmable-logic/arty-a7/arty_a7_sch.pdf

-----------------------------

## Teoretický návrh tachometru

Tachometr je ovládán jedním přepínačem a jedním tlačítkem

- Přepínač: 
Přepínání mezi zobrazovanými veličinami. P/okud je přepínač v "nule" zobrazí se ujetá vzdálenost. Když je přepnut do "jedničky" na display se zobrazí momentální rychlost. 

- Tlačítko:
Resetování celého zařízení, vynulování přičítání ujeté vzdálenosti. 

Zobrazení na display: 
Přepínání zobrazených veličin na display zařizuje stavový automat ovládaný přepínačem. Jednotlivé číslice pak  4 to 1 multiplexor - ovládaný čítečem - posílá na jednotlivé displaye. Rychlost přepínaných číslic je větší než rozlišovací schopnost lidského oka a proto se číslice jeví zobrazeny naráz. (Postup pro zobrazení dat ze cvičení). 

### Schéma top modulu

shéma top modulu s vyznačenými signály a dílčími moduly

![topmodul]()

------------------------

## Design moduly a jejich popis

### Velocity

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/Velocity_design.md)


### Distance

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/Design_distance.md)

### Clock enable

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/clock_enable.md)

### Count up down

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/count_up_down.md)

### FSM

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/fsm.md)

### mux_4_to_1

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/mux.md)

### hex_7seg

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/hex_to_7seg.md)


## Testbench moduly a simulace

### Velocity

![Simulace velocity]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_distance.md)


### Distance

![simulace distance]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_distance.md)

### Clock enable

![simulace clock enable]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_clock%20enable.md)

### Count up down

![simulace čítač]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_%C4%8D%C3%ADta%C4%8D.md)

### FSM

![simulace fsm]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_fsm.md)

### mux_4_to_1

![simulace multiplexor]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_mux.md)

### hex_7seg

![simulace hex_7seg]()

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_hex_7seg.md)

------------------------

## Constrait file


## Bit Stream

-----------------------




