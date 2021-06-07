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

Pro zjednodušení výpočtů jsme stanovili konstantní obvod kola dva metry. 

### Schéma top modulu

shéma top modulu s vyznačenými signály a dílčími moduly

![topmodul](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/scheme_2.jpg)

------------------------

## Design moduly a jejich popis

### Velocity

Modul počítající momentální rychlost jízdy. Čas je přičítán z hodinového signálu. Když dojde k vypuštění signálu z hallovy sondy, přičte se do proměnné metry obvod kola. Z času a ujeté vzdálenosti je následně vypočítaná rychlost převedená na km/h a pomocí funkce modulo rozdělena na desítky metrů, stovky metrů, kilometry a desítky kilometrů.  
Čas v modulu je určen tak aby dobře vypadala simulace, pro reálné použití by bylo třeba zmenšit přičítané časové úseky a nahradit je hodinovým signálem desky.
Při stlačení tlačítka reset, se signál jdoucí na dispklay vynuluje. 

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/Velocity_design.md)


### Distance

Modul distance funguje obdobně jako modul velocity. Pouze je vypuštěno přičítání času a výpočet rychlosti. Při průchodu hallovy sondy senzorem, vyšle sensor signál v hodnotě 1. když je náběžná hrana signálu clk, přičte se do proměnné metry obvod kola. 
Rozřazení jednotlivých digitů informace z modulu je provedeno pomocí funkce modulo.

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/Design_distance.md)

### Clock enable

Modul pro "zpomalení" signálu clock. Pokud je signál s_en v hodnotě 1, moduly berou náběžnou hranu signálu clk. Tím nedojde k přetečení jednotlivýách modulů, vyžadujících pomalejší hodinový signál. 

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/clock_enable.md)

### Count up down

Čítač, který přepíná multiplexor. Freekvence čítání je zvolena tak aby docházelo k iluzi stále zobrazovaného digitu. 

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/count_up_down.md)

### FSM

Pomocí jednoducháho stavového automatu se přepíná mezi dvěma zobrazovanými veličinami na display. Mezi dvěma stavy automatu se přepíná pomocáí switche. Stav v nule posílá dál data z modulu distance, stav v jedniče zase data z modulu velocity.

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/fsm.md)

### mux_4_to_1

Multiplexor napojený na čítač posíla hodnoty jednotlivých digitů na řadič displaye. 

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/mux.md)

### hex_7seg

Řadič informace na sedmisegmentový display. 

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/hex_to_7seg.md)


## Testbench moduly a simulace

### Velocity

![Simulace velocity](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_velocity.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/velocity_tb.md)


### Distance

![simulace distance](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_distance.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_distance.md)

### Clock enable

![simulace clock enable](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_clock_enable.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_clock%20enable.md)

### Count up down

![simulace čítač](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_cnt_up_down.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_%C4%8D%C3%ADta%C4%8D.md)

### FSM

![simulace fsm](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_FSM.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_fsm.md)

### mux_4_to_1

![simulace multiplexor](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_mux_4to1.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_mux.md)

### hex_7seg

![simulace hex_7seg](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_hex_7seg.PNG)

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_hex_7seg.md)

------------------------

## Top

Entita top byla udělána dvoustupňově. Prvně jsme propojili jednotlivé moduly v entitě bike_sensor a následně jsme celou entitu namapovali na desku pomocí entity top. 

### Bike sensor

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/bike_sensor.md)

![simulace bike sensor](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/pictures/screen_bike_sensor.PNG)

[Odkaz na kód testbench](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/tb_bike_sensor.md)



### Top

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/top.md)

-----------------------

## Constrait file

[Odkaz na kód](https://github.com/JanMostecky/Digital-electronics-1/blob/main/projekt%20-%20oprava/k%C3%B3dy/constraint.md)

## Bit Stream

-----------------------

## Diskuze

Podařilo se nám opravit jednoduchý tachometr na jízdní kolo. Konzole obsahuje dva moduly pro určení momentální rychlosti a ujeté vzdálenosti. Zařízení se ovládá dvěma přepínači. Prvním z nich přepínáme mezi zobrazením měřících módů na display a druhým resetujeme celé zařízení, kde dojde k vynulování ujeté vzdálenosti. 
Zobrazení veličin zajišťuje externí čtyřpanelový sedmisegmentový display ovládaný pomocí fsm, multiplexoru a čítače. 

Hlavní problém předchozí verze projektu bylo rozřazení jednotlivých digitů informace na čtyřmístný sedmisegmentový display. To jsme vyřešili pomocí funkce modulo v modulech distance a velocity. 

V konečné fázy projektu se nám nepodařilo vygenerovat bitstream. Syntéza i Implementace proběhly v pořádku (víc, viz složka s projektem). Nepřišli jsme na chybu, která zabránila vyenerování bitstreamu. 



