### Design kód modulu Distance

```vhdl

library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;


entity distance is
    generic(
    perimeter : natural := 2    
            );
            
    Port (
           clk       :  in   std_logic;
           reset     :  in   std_logic;      --
           sensor_i  :  in   std_logic;      --
           dist0_o   :  out  std_logic_vector(4 - 1 downto 0);
           dist1_o   :  out  std_logic_vector(4 - 1 downto 0);
           dist2_o   :  out  std_logic_vector(4 - 1 downto 0);
           dist3_o   :  out  std_logic_vector(4 - 1 downto 0)
          );
end distance;

architecture Behavioral of distance is
    signal metry : natural := 0;
    signal s_wait: std_logic;
begin

    p_cnt_distance : process(clk)
    begin
        if rising_edge(clk) then
        if(sensor_i = '1' and s_wait = '0')then
            metry <= metry + perimeter;
            s_wait <= '1';
            if (reset = '1' or metry >= 100000) then -- Reset
                    metry <= 0;
                    dist0_o <= "0000";
                    dist1_o <= "0000";
                    dist2_o <= "0000";
                    dist3_o <= "0000";
            elsif (reset = '0') then
                    dist0_o <= std_logic_vector(to_unsigned((metry mod 100 - (metry mod 10))/10,4));      
                    dist1_o <= std_logic_vector(to_unsigned((metry mod 1000 - (metry mod 100))/100,4));   
                    dist2_o <= std_logic_vector(to_unsigned((metry mod 10000 - (metry mod 1000))/1000,4));
                    dist3_o <= std_logic_vector(to_unsigned((metry - (metry mod 10000))/10000,4));        
            end if;
        elsif(sensor_i = '0')then
            s_wait <= '0';
        end if;
        end if;      
    end process p_cnt_distance;


end Behavioral;

```
