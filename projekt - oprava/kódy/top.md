### Top kód

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity top is
    Port ( 
           CLK100MHZ    : in  STD_LOGIC;     
           -- BTNC         : in  STD_LOGIC;
           SW           : in  STD_LOGIC_VECTOR (2 - 1 downto 0);
           SE           : in STD_LOGIC;
           CA           : out STD_LOGIC;
           CB           : out STD_LOGIC;
           CC           : out STD_LOGIC;
           CD           : out STD_LOGIC;
           CE           : out STD_LOGIC;
           CF           : out STD_LOGIC;
           CG           : out STD_LOGIC;
           DP           : out STD_LOGIC;
           AN           : out STD_LOGIC_VECTOR (4 - 1 downto 0)
           );
end top;

------------------------------------------------------------------------
-- Architecture body for top level
------------------------------------------------------------------------
architecture Behavioral of top is

    -- Internal clock enable
    signal s_en  : std_logic;
    -- Internal counter
    signal s_cnt : std_logic_vector(4 - 1 downto 0);

begin

    --------------------------------------------------------------------
    -- Instance (copy) of bike_sensor entity
    --------------------------------------------------------------------
    bike_sensor : entity work.bike_sensor
        port map(
           clk         => CLK100MHZ, 
           reset       => SW (1),
           BTN         => SW (0),
           sensor_i    => SE,
           seg_o(6)    => CA,
           seg_o(5)    => CB,
           seg_o(4)    => CC,
           seg_o(3)    => CD,
           seg_o(2)    => CE,
           seg_o(1)    => CF,
           seg_o(0)    => CG,   
           dig_o       => AN,
           dp_o        => DP                                                                                                       
        );  




end architecture Behavioral;

```
