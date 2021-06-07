### Testbench pro distance

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;


entity tb_distance is

end tb_distance;

architecture Behavioral of tb_distance is

constant c_CLK_100MHZ_PERIOD : time    := 10 ns;

signal s_clk       : std_logic;                       
signal s_reset     : std_logic;                    
signal s_sensor_i  : std_logic;                    
signal s_dist0_o   : std_logic_vector(4 - 1 downto 0);
signal s_dist1_o   : std_logic_vector(4 - 1 downto 0);
signal s_dist2_o   : std_logic_vector(4 - 1 downto 0);
signal s_dist3_o   : std_logic_vector(4 - 1 downto 0);



begin

uut_distance : entity work.distance
    port map
    (
        clk      => s_clk,    
        reset    => s_reset,   
        sensor_i => s_sensor_i,
        dist0_o  => s_dist0_o, 
        dist1_o  => s_dist1_o, 
        dist2_o  => s_dist2_o,
        dist3_o  => s_dist3_o 
     );
p_clk_gen : process
       begin
           while now < 50000 ns loop
               s_clk <= '0';
               wait for c_CLK_100MHZ_PERIOD / 2;
               s_clk <= '1';
               wait for c_CLK_100MHZ_PERIOD / 2;
           end loop;
           wait;
       end process p_clk_gen;
       
    p_reset_gen : process
        begin
            s_reset <= '0';
            wait for 45000 ns;
            s_reset <= '1';
            wait for 500 ns;
            s_reset <= '0';
            wait;
        end process p_reset_gen;
        
   p_stimulus : process         
    begin
        report "Stimulus process started" severity note;
            while now < 50000 ns loop
               s_sensor_i <= '0';
               wait for 250 ns;
               s_sensor_i <= '1';
               wait for 13 ns;
           end loop;
           wait;
        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;
end Behavioral;
```
