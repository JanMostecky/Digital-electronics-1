### Kód pro multiplexor

```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

------------------------------------------------------------------------
-- Entity declaration for display driver
------------------------------------------------------------------------
entity mux is
    port(
        cnt_i   : in  std_logic_vector(2 - 1 downto 0);
        -- 4-bit input values for individual digits
        data0_i : in  std_logic_vector(4 - 1 downto 0);
        data1_i : in  std_logic_vector(4 - 1 downto 0);
        data2_i : in  std_logic_vector(4 - 1 downto 0);
        data3_i : in  std_logic_vector(4 - 1 downto 0);
        -- 4-bit input value for decimal points
        dp_o    : out std_logic;
        -- Cathode values for individual segments
        seg_o   : out std_logic_vector(4 - 1 downto 0);
        -- Common anode signals to individual displays
        dig_o   : out std_logic_vector(4 - 1 downto 0)
    );
end entity mux;

------------------------------------------------------------------------
-- Architecture declaration for display driver
------------------------------------------------------------------------
architecture Behavioral of mux is
begin
    p_mux : process(cnt_i, data0_i, data1_i, data2_i, data3_i)
    begin
        case cnt_i is
            when "11" =>
                seg_o <= data3_i;
                dig_o <= "0111";

            when "10" =>
                seg_o <= data2_i;
                dp_o  <= '1';
                dig_o <= "1011";

            when "01" =>
                seg_o <= data1_i;
                dig_o <= "1101";

            when others =>
                seg_o <= data0_i;
                dig_o <= "1110";
                
        end case;
    end process p_mux;

end architecture Behavioral;
```
