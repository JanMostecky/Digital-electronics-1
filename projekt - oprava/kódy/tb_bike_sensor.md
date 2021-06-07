### Testbench entity bike sensor

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity tb_bike_sensor is
--  Port ( );
end tb_bike_sensor;

architecture Behavioral of tb_bike_sensor is

     constant c_CLK_100MHZ_PERIOD : time    := 10 ns;                 
     
     signal s_clk     : std_logic;        -- Main clock
     signal s_reset   : std_logic;        -- Synchronous reset
     signal s_BTN     : std_logic;
     signal s_sensor_i: std_logic;
     signal s_seg_o   : std_logic_vector(7 - 1 downto 0);    -- Cathode values for individual segments
     signal s_dig_o   : std_logic_vector(4 - 1 downto 0);
 
begin

    uut_bike_sensor : entity work.bike_sensor
            
            port map(
              clk      =>  s_clk,     
              reset    =>  s_reset,   
              BTN      =>  s_BTN,     
              sensor_i =>  s_sensor_i,
              seg_o    =>  s_seg_o,   
              dig_o    =>  s_dig_o 
            );
   --Clock generation process--  
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
            wait;
        end process p_reset_gen;
   p_BTN_gen : process
        begin
            s_BTN <= '0';
            wait for  25000 ns;
            s_BTN <= '1';
            wait;
        end process p_BTN_gen;
   --Data generation process--    
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
