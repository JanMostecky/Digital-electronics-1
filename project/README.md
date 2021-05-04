# Tachometer

### vypracovali:


Klimeš Jiří (),
Mostecký Jan (),
Němec Petr (),
Januška Tomáš ()

[Github project folder]( https://github.com/JanMostecky/Digital-electronics-1/tree/main/project)



-----------------------------------
## Cíl projektu

Cílem projektu bylo navrhnout a realizovat pomocí jazyku VHDL cyklo-tachometr. Následně výsledek projektu zpracovat do README.md souboru a videoprezentace. 

Funkce tachometru: měření rychlosti, Měření ujeté vzdálenosti.


-----------------------------------
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


### Vstupy a výstupy desky Arty A7-35T:

deska Arty A7-35T
![BoardImage](https://github.com/JanMostecky/Digital-electronics-1/blob/main/project/pictures/Arty_A7_Board.PNG)

Popis desky
![BoardIO](https://github.com/JanMostecky/Digital-electronics-1/blob/main/project/pictures/Board_Description.png)

Základní vstupy a výstupy
![BoardDescription](https://github.com/JanMostecky/Digital-electronics-1/blob/main/project/pictures/Basic_IO.PNG)


Available for example here:
https://www.aliexpress.com/i/32367486295.html

That display would include colon (double dot, ":") between hours, minutes and seconds.
And while measuring distance and velocity, colon wouldn't be displayed.


-----------------------------------
## Teoretický návrh tachometru

Poznámka: Nikdo z našeho týmu dřív s jazykem VHDL nepracoval, máme minimální zkušenosti s programováním a nikdo z nás se mu aktivně nevěnuje ani v jiném jazyce. S programovatelnou deskou sme se nikdy nesetkali osobně.

Pro implementaci - vzhledem k našim začátečnických schopnostem ve VHDL - bylo rozhodnuto o ovládání tachometru jedním přepínačem a dvěma tlačítky. 

- Přepínač: zapínání a vypínání celého zařízení.

- Tlačítko A:
Přepínání zobrazených modulů na display.  

- Tlačítko B:
Resetování celého zařízení, vynulování přičítání ujeté vzdálenosti. 

Zobrazení na display: 
Pro tuto funkci byly navrženy dva různé moduly. První verze bylo použití stavového automatu, druhá verze přepínala moduly na display za použití multiplexoru. Finální verze projektu pracuje s použitím stavového automatu. 
Pro zobrazení od 0 do 9999 ujetých kilometrů byly navrženy čtyři 7segment displaye. Toto zapojení by teké umožnilo měřit čas od 0 do 99:60. Poté se čas resetuje. 
Lepší provedení by bylo za použití šesti 7segment displayů pro zobrazení hodin a více ujetých kilomtrů. 

### Schéma top modulu




-----------------------------------
## VHDL moduly a jejich popis

V každé podkapitole jdou za sebou design kód a testbench kód. 

### On_off modul

On_off modul slouží ke spouštění celého systému pomocí přepínače. 

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity on_off is
      Port (
            start_i : in  std_logic;     -- Input from Switch (0) to turn on/off the tachometer
            start_o : out std_logic     -- Output that starts other modules
            );
end on_off;

architecture Behavioral of on_off is

begin

      p_on_off : process(start_i)       -- When start_i changes ( we change the position of SW(0), the process is triggered)
         begin
            if (start_i = '1') then         -- Switch in position ON (or position 1)
                start_o <= '1';                 -- Output is 1
            else     -- Switch in position OFF (or 0)
                start_o <= '0';                 -- Output is 0
            end if;
         end process p_on_off;

end Behavioral;

```

```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

entity tb_on_off is
--  Port ( );
end tb_on_off;

architecture Behavioral of tb_on_off is

        signal s_start_i      : std_logic;
        signal s_start_o      : std_logic;

begin
    uut_on_off : entity work.on_off
       
     port map(
            start_i  => s_start_i,
            start_o  => s_start_o
             );

    --------------------------------------------------------------------
    -- Data generation process
    --------------------------------------------------------------------
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;
        
        s_start_i     <= '0';
        wait for 100 ns;        
        s_start_i     <= '1';
        wait for 200 ns;        
        s_start_i     <= '0';
        wait for 20 ns;       
        s_start_i     <= '1';
        wait for 300 ns;        
        s_start_i     <= '0';
        wait for 200 ns;        
        s_start_i     <= '1';
        wait for 150 ns;

        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;

end Behavioral;

```

### Clock enable

Modul generující clock signál pro sinchronizaci výpořtu rychlosti

```vhdl
library ieee;               -- Standard library
use ieee.std_logic_1164.all;-- Package for data types and logic operations
use ieee.numeric_std.all;   -- Package for arithmetic operations

------------------------------------------------------------------------
-- Entity declaration for clock enable
------------------------------------------------------------------------
entity clock_enable is
    generic(
        g_MAX : natural := 10       -- Number of clk pulses to generate
                                    -- one enable signal period
    );  -- Note that there IS a semicolon between generic and port
        -- sections
    port(
        clk   : in  std_logic;      -- Main clock
        reset : in  std_logic;      -- Synchronous reset
        ce_o  : out std_logic       -- Clock enable pulse signal
    );
end entity clock_enable;

------------------------------------------------------------------------
-- Architecture body for clock enable
------------------------------------------------------------------------
architecture Behavioral of clock_enable is

    -- Local counter
    signal s_cnt_local : natural;

begin
    --------------------------------------------------------------------
    -- p_clk_ena:
    -- Generate clock enable signal. By default, enable signal is low 
    -- and generated pulse is always one clock long.
    --------------------------------------------------------------------
    p_clk_ena : process(clk)
    begin
        if rising_edge(clk) then        -- Synchronous process

            if (reset = '1') then       -- High active reset
                s_cnt_local <= 0;       -- Clear local counter
                ce_o        <= '0';     -- Set output to low

            -- Test number of clock periods
            elsif (s_cnt_local >= (g_MAX - 1)) then
                s_cnt_local <= 0;       -- Clear local counter
                ce_o        <= '1';     -- Generate clock enable pulse

            else
                s_cnt_local <= s_cnt_local + 1;
                ce_o        <= '0';
            end if;
        end if;
    end process p_clk_ena;
    ```

```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

------------------------------------------------------------------------
-- Entity declaration for testbench
------------------------------------------------------------------------
entity tb_clock_enable is
    -- Entity of testbench is always empty
end entity tb_clock_enable;

------------------------------------------------------------------------
-- Architecture body for testbench
------------------------------------------------------------------------
architecture testbench of tb_clock_enable is

    constant c_MAX               : natural := 8;
    constant c_CLK_100MHZ_PERIOD : time    := 10 ns;

    --Local signals
    signal s_clk_100MHz : std_logic;
    signal s_reset      : std_logic;
    signal s_ce         : std_logic;

begin
    -- Connecting testbench signals with clock_enable entity
    -- (Unit Under Test)
    uut_ce : entity work.clock_enable
        generic map(
            g_MAX => c_MAX
        )   -- Note that there is NO comma or semicolon between
            -- generic map section and port map section
        port map(
            clk   => s_clk_100MHz,
            reset => s_reset,
            ce_o  => s_ce
        );

    --------------------------------------------------------------------
    -- Clock generation process
    --------------------------------------------------------------------
    p_clk_gen : process
    begin
        while now < 750 ns loop         -- 75 periods of 100MHz clock
            s_clk_100MHz <= '0';
            wait for c_CLK_100MHZ_PERIOD / 2;
            s_clk_100MHz <= '1';
            wait for c_CLK_100MHZ_PERIOD / 2;
        end loop;
        wait;                           -- Process is suspended forever
    end process p_clk_gen;

    --------------------------------------------------------------------
    -- Reset generation process
    --------------------------------------------------------------------
    p_reset_gen : process
    begin
        s_reset <= '0';
        wait for 28 ns;
        
        -- Reset activated
        s_reset <= '1';
        wait for 153 ns;

        -- Reset deactivated
        s_reset <= '0';

        wait;
    end process p_reset_gen;

    --------------------------------------------------------------------
    -- Data generation process
    --------------------------------------------------------------------
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;

        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;

end architecture testbench;
```

### Sensor modul

Sensor modul slouží k zaznamenávání a následnému předávání signálu z halovy sondy dál do systému k výpočtu rychlosti a ujeté vzdálenosti. 

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;


entity sensor is
  Port (
        input    : in  std_logic;
        en_i     : in  std_logic;
        reset    : in std_logic;
        
        revs_o   : out std_logic_vector(13 downto 0)
         );
end sensor;

architecture Behavioral of sensor is
    
    signal s_cnt_local : natural;
    
begin
    
    p_cnt_up_down : process(input, reset)
    begin
        
            if (reset = '1') then               -- Synchronous reset
                s_cnt_local <= 0;
            elsif (en_i = '1') then       -- Test if counter is enabled
                    -- TEST COUNTER DIRECTION HERE
                if rising_edge(input) then
                    if (s_cnt_local = 5000) then
                        s_cnt_local <=0;
                    else
                    s_cnt_local <= s_cnt_local + 1;
                    end if;
            end if;

        end if;
    end process p_cnt_up_down;

    revs_o <= std_logic_vector(to_unsigned(s_cnt_local, revs_o'length));

end Behavioral;

```

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;


entity tb_sensor is

end tb_sensor;

architecture Behavioral of tb_sensor is

        signal s_input      : std_logic;
        signal s_en_i       : std_logic;
        signal s_reset      : std_logic;
        signal s_revs       : std_logic_vector(13 downto 0);

begin
    uut_on_off : entity work.sensor

     port map(
            input  => s_input,
            en_i   => s_en_i,
            reset  => s_reset,
            revs_o   => s_revs
             );

    --------------------------------------------------------------------
    -- Reset generation process
    --------------------------------------------------------------------
    p_reset_gen : process
    begin
        s_reset <= '0';
        wait for 20 ns;
        
        -- Reset activated
        s_reset <= '0';
        wait for 30 ns;

        s_reset <= '0';
        wait for 700 ns;
        
        s_reset <= '0';
        wait for 70 ns;
        
        s_reset <= '0';
        
        wait;
    end process p_reset_gen;
    


    --------------------------------------------------------------------
    -- Data generation process
    --------------------------------------------------------------------
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;
        
        s_en_i     <= '1';
        
        s_input    <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;    
        s_input     <= '0';
        wait for 10 ns;        
        s_input     <= '1';    
        wait for 10 ns;       
        s_input     <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;
        
        s_en_i      <='0';
        
        s_input    <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;        
        s_input     <= '0';
        wait for 10 ns;        
        s_input     <= '1';    
        wait for 10 ns;       
        s_input     <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;
        
        
        s_en_i     <= '1';
        
        s_input    <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;        
        s_input     <= '0';
        wait for 10 ns;    
        s_en_i     <= '0';    
        s_input     <= '1';    
        wait for 10 ns;       
        s_input     <= '0';
        wait for 10 ns;    
        s_en_i     <= '1';    
        s_input     <= '1';
        wait for 10 ns;
        
        s_input    <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;        
        s_input     <= '0';
        wait for 10 ns;        
        s_input     <= '1';    
        wait for 10 ns;       
        s_input     <= '0';
        wait for 10 ns;        
        s_input     <= '1';
        wait for 10 ns;
        
        
        s_input    <= '0';
        wait for 50 ns;        
        s_input     <= '1';
        wait for 40 ns;        
        s_input     <= '0';
        wait for 20 ns;        
        s_input     <= '1';    
        wait for 30 ns;       
        s_input     <= '0';
        wait for 70 ns;        
        s_input     <= '1';
        wait for 60 ns;
        
        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;

end Behavioral;
```

### Distance modul

Modul distance slouží k výpočtu ujeté vzdálenosti
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;


entity distance is
    generic(
    perimeter : natural := 2    
            );
            
    Port (
           reset    :  in   std_logic;
           en_i     :  in   std_logic;
           revs_i   :  in   natural;
           dist     :  out  natural
          );
end distance;

architecture Behavioral of distance is


begin

    p_cnt_distance : process(revs_i)
    begin
        if (reset = '1') then               -- Reset
                dist <= 0;
            elsif (en_i = '1') then       -- Test if counter is enabled
                if revs_i = 5000 then
                    dist<=0;
                else
                dist <= revs_i * perimeter;
                end if;
            end if;
            
    end process p_cnt_distance;


end Behavioral;

```

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;


entity tb_distance is

end tb_distance;

architecture Behavioral of tb_distance is

        constant c_perimeter    : natural := 2;

        signal s_reset          : std_logic;
        signal s_en_i           : std_logic;
        signal s_revs           : natural;
        signal s_dist           : natural;

begin
    uut_distance : entity work.distance

     port map(
           reset   => s_reset,
           en_i    => s_en_i,
           revs_i  => s_revs,
           dist    => s_dist
             );

    --------------------------------------------------------------------
    -- Reset generation process
    --------------------------------------------------------------------
    p_reset_gen : process
    begin
        s_reset <= '0';
        wait for 40 ns;
        
        -- Reset activated
        s_reset <= '1';
        wait for 60 ns;

        s_reset <= '0';
        wait for 700 ns;
        
        s_reset <= '1';
        wait for 70 ns;
        
        s_reset <= '0';
        
        wait;
    end process p_reset_gen;
    


    --------------------------------------------------------------------
    -- Data generation process
    --------------------------------------------------------------------
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;
        
        s_en_i     <= '1';
        
        s_revs    <= 0;
        wait for 20 ns;        
        s_revs     <= 1;
        wait for 20 ns;    
        s_revs     <= 0;
        wait for 20 ns;        
        s_revs     <= 0;    
        wait for 20 ns;       
        s_revs     <= 0;
        wait for 20 ns;        
        s_revs     <= 4997;
        wait for 20 ns;
        s_revs     <= 4998;
        wait for 20 ns;
        s_revs     <= 4999;
        wait for 20 ns;
        s_revs     <= 5000;
        wait for 20 ns;
        s_revs     <= 0;
        wait for 20 ns;
        s_revs     <= 1;
        wait for 20 ns;
        

        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;

end Behavioral;
```

### Velocity modul

Modul velocity slouží k výpočtu momentální rychlosti jízdy. 

```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

entity velocity is
  generic(
          g_CNT_WIDTH     : natural := 22;--;            -- Number of bits for counter
          g_circumference : natural := 2;--m            --standart 26' wheel circrumference
          g_clk_frequency : natural := 50000000--Hz     --Frequency of signal from main clock
          );
  port   (
            clk           : in  std_logic;                                  -- Main clock
            reset         : in  std_logic;                                  -- Synchronous reset signal from hall sensor
            en_i          : in  std_logic;                                  -- enable signal from on_off module
            period_count  : in  std_logic_vector(g_CNT_WIDTH - 1 downto 0);  -- input from counter measuring period of rotation
            velocity_o    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0)  -- output for hex_deg       
          );
end velocity;

architecture Behavioral of velocity is

signal s_count    : integer;
signal s_velocity : integer;

begin
--converting period_count to integer in order to count velocity
s_count <= to_integer(unsigned(period_count));
------------------------------------------------------
--  Process for counting velocity with 8-bit output --
------------------------------------------------------
    p_velocity_counter : process (clk, reset)
    begin
       if (rising_edge(clk)) then --synchronous process
       --signal from hall sensor resets the input value of period_count from cnt_up module
            if en_i = '1' then 
       --physical definition of velocity using known number of periods between
       --two hall sensor impulses and circumference - distance between those impulses
                s_velocity <=  ( g_circumference * g_clk_frequency) / (s_count);
--            else
--       --no change
--                s_velocity <= s_velocity;
            end if;
        end if;
        velocity_o <= std_logic_vector(to_unsigned(s_velocity, velocity_o'length));
    end process p_velocity_counter;
end Behavioral;

```

### řazení modulů na display pomocí multiplexoru

Tento modun nebyl v konečné realizaci použit. Pro funkčnost tohoto modulu by bylo zapotřebí pro zobrazení druhého modulu tlačítko držet, případně realizovat přepínání za pomocí switche. 

```vhdl
library ieee;
use ieee.std_logic_1164.all;


entity fsm is
    port(
        button_1        : in std_logic;
        vel_i           : in std_logic_vector (8 - 1 downto 0);
        dis_i           : in std_logic_vector (14 - 1 downto 0);
        to_display      : out std_logic_vector (14 - 1 downto 0)
        
    );
end entity fsm;


architecture Behavioral of fsm is
begin

 p_mux : process (vel_i, dis_i, button_1)
    begin
        case button_1 is
            when '0' =>
                to_display <= vel_i;
                
            when '1' =>
                to_display <= dis_i;
                
            when others =>
                to_display <= vel_i;                            
                            
        end case;
    end process p_mux;
end architecture Behavioral;
```

```vhdl
library ieee;
use ieee.std_logic_1164.all;


entity tb_fsm is
    
end entity tb_fsm;


architecture testbench of tb_fsm is

  

        signal s_button_1        :  std_logic;
        signal s_vel             :  std_logic_vector (8 - 1 downto 0);
        signal s_dis_i           :  std_logic_vector (14 - 1 downto 0);
        signal s_to_display      :  std_logic_vector (14 - 1 downto 0);
        

begin
    
    uut_fsm : entity work.fsm
        port map(
            button_1        => s_button_1,
            vel_i           => s_vel,
            dis_i           => s_dis_i,
            to_display      => s_to_display
        );

    
    p_stimulus : process
    begin
       
        report "Stimulus process started" severity note;

        s_button_1 <= '0'; wait for 100 ns;
        s_button_1 <= '1'; wait for 100 ns;
        
        wait;
   end process p_stimulus;
       
end architecture testbench;
```

-----------------------------------
## TOP module description and simulations

Write your text here.



-----------------------------------
## Video

*Write your text here*



-----------------------------------
## References

   1. Write your text here.
