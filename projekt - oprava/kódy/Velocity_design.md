### Design kód modulu velocity

```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

entity velocity is
  generic(
          perimeter : natural := 2--m            --standart 26' wheel circrumference
          );
  port   (
            clk           : in  std_logic; -- Main clock
            reset         : in  std_logic;
            sensor_i      : in  std_logic;                                  -- enable signal from on_off module
            vel0_o        : out std_logic_vector(4 - 1 downto 0);
            vel1_o        : out std_logic_vector(4 - 1 downto 0);
            vel2_o        : out std_logic_vector(4 - 1 downto 0);
            vel3_o        : out std_logic_vector(4 - 1 downto 0)  -- output for hex_deg       
          );
end velocity;

architecture Behavioral of velocity is

signal s_count_sec    : natural;
signal s_wait         : std_logic;
signal metry          : natural := 0;
begin

    p_velocity_counter : process (clk)
    begin
       if (rising_edge(clk)) then
            s_count_sec <= s_count_sec + 1;
                if(s_count_sec >= 100)then
                    vel0_o      <= std_logic_vector(to_unsigned((metry * 360 mod 100 - (metry * 360 mod 10))/10,4));
                    vel1_o      <= std_logic_vector(to_unsigned((metry * 360 mod 1000 - (metry * 360 mod 100))/100,4));
                    vel2_o      <= std_logic_vector(to_unsigned((metry * 360 mod 10000 - (metry * 360 mod 1000))/1000,4));
                    vel3_o      <= std_logic_vector(to_unsigned((metry * 360 - (metry * 360 mod 10000))/10000,4));
                    s_count_sec <= 0; 
                    metry       <= 0;
               elsif (reset = '1') then -- Reset
                    metry  <= 0;
                    vel0_o <= "0000";
                    vel1_o <= "0000";
                    vel2_o <= "0000";
                    vel3_o <= "0000";
                elsif (reset = '0' and sensor_i = '1' and s_wait = '0') then
                    s_wait <= '1';
                    metry <= metry + perimeter;
                elsif(sensor_i = '0')then
                    s_wait <= '0';  
                end if;
        end if;
    end process p_velocity_counter;
end Behavioral;


```
