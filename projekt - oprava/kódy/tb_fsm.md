### Testbench stavového automatu

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity tb_fsm is

end tb_fsm;

architecture Behavioral of tb_fsm is

     constant c_CLK_100MHZ_PERIOD : time    := 10 ns;                 
     
     signal s_btn     : std_logic;                       
     signal s_clk     : std_logic;                     
     signal s_vel0_i  : std_logic_vector(4 - 1 downto 0);
     signal s_vel1_i  : std_logic_vector(4 - 1 downto 0);
     signal s_vel2_i  : std_logic_vector(4 - 1 downto 0);
     signal s_vel3_i  : std_logic_vector(4 - 1 downto 0);
     signal s_dist0_i : std_logic_vector(4 - 1 downto 0);
     signal s_dist1_i : std_logic_vector(4 - 1 downto 0);
     signal s_dist2_i : std_logic_vector(4 - 1 downto 0);
     signal s_dist3_i : std_logic_vector(4 - 1 downto 0);
     signal s_data0_o : std_logic_vector(4 - 1 downto 0);
     signal s_data1_o : std_logic_vector(4 - 1 downto 0);
     signal s_data2_o : std_logic_vector(4 - 1 downto 0);
     signal s_data3_o : std_logic_vector(4 - 1 downto 0);
 
begin

    uut_bike_sensor : entity work.fsm
            
            port map(
              btn     =>  s_btn,    
              clk     =>  s_clk,    
              vel0_i  =>  s_vel0_i, 
              vel1_i  =>  s_vel1_i, 
              vel2_i  =>  s_vel2_i, 
              vel3_i  =>  s_vel3_i,
              dist0_i =>  s_dist0_i,
              dist1_i =>  s_dist1_i,
              dist2_i =>  s_dist2_i,
              dist3_i =>  s_dist3_i,
              data0_o =>  s_data0_o,
              data1_o =>  s_data1_o,
              data2_o =>  s_data2_o,
              data3_o =>  s_data3_o
            );
   --Clock generation process--  
    p_clk_gen : process
       begin
           while now < 1000 ns loop
               s_clk <= '0';
               wait for c_CLK_100MHZ_PERIOD / 2;
               s_clk <= '1';
               wait for c_CLK_100MHZ_PERIOD / 2;
           end loop;
           wait;
       end process p_clk_gen;
   p_BTN_gen : process
        begin
            s_BTN <= '0';
            wait for  500 ns;
            s_BTN <= '1';
            wait;
        end process p_BTN_gen;
   --Data generation process--    
    p_stimulus : process         
    begin
        report "Stimulus process started" severity note;
           s_vel0_i  <= "1111";
           s_vel1_i  <= "1110";
           s_vel2_i  <= "1100";
           s_vel3_i  <= "1000";
           s_dist0_i <= "1111";
           s_dist1_i <= "0111";
           s_dist2_i <= "0011";
           s_dist3_i <= "0001";
           wait;
        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;
end Behavioral;


```
