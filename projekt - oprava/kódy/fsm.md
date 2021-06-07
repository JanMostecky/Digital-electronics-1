### Kód pro FSM

```vhdl

library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;


entity fsm is
    port(
        btn      : in  std_logic;
        clk      : in  std_logic;
        vel0_i   : in  std_logic_vector(4 - 1 downto 0);
        vel1_i   : in  std_logic_vector(4 - 1 downto 0);
        vel2_i   : in  std_logic_vector(4 - 1 downto 0);
        vel3_i   : in  std_logic_vector(4 - 1 downto 0);
        dist0_i  : in  std_logic_vector(4 - 1 downto 0);
        dist1_i  : in  std_logic_vector(4 - 1 downto 0);
        dist2_i  : in  std_logic_vector(4 - 1 downto 0);
        dist3_i  : in  std_logic_vector(4 - 1 downto 0);
        data0_o  : out std_logic_vector(4 - 1 downto 0);
        data1_o  : out std_logic_vector(4 - 1 downto 0);
        data2_o  : out std_logic_vector(4 - 1 downto 0);
        data3_o  : out std_logic_vector(4 - 1 downto 0)
        
    );
end entity fsm;


architecture Behavioral of fsm is

    -- Define the states
    type t_state is (                     
                     DISTANCE,
                     VELOCITY
                     );
    signal present_state : t_state;
begin
    p_fsm : process(clk)
    begin
        if rising_edge(clk) then
            if(btn = '0')then
                present_state <= DISTANCE;
            elsif(btn = '1')then
                present_state <= VELOCITY;
            end if;
            case present_state is
                when DISTANCE =>
                     data0_o <= dist0_i;
                     data1_o <= dist1_i;
                     data2_o <= dist2_i;
                     data3_o <= dist3_i;
                when VELOCITY =>
                     data0_o <= vel0_i;
                     data1_o <= vel1_i;
                     data2_o <= vel2_i;
                     data3_o <= vel3_i;
            end case;
        end if;    
    end process p_fsm;   
end architecture Behavioral;


```
