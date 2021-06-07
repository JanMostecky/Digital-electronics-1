### Kód entity bike sensor

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

entity bike_sensor is
    port(
        clk     : in  std_logic;        -- Main clock
        reset   : in  std_logic;        -- Synchronous reset
        BTN     : in  std_logic;
        sensor_i: in  std_logic;
        dp_o    : out std_logic;
        seg_o   : out std_logic_vector(7 - 1 downto 0);    -- Cathode values for individual segments
        dig_o   : out std_logic_vector(4 - 1 downto 0)
        
    );
end entity bike_sensor;

architecture behavioral of bike_sensor is
    
    signal s_en    : std_logic;                           -- Internal clock enable
    signal s_cnt   : std_logic_vector(2 - 1 downto 0);    -- Internal 2-bit counter for multiplexing 4 digits
    signal s_hex   : std_logic_vector(4 - 1 downto 0);    -- Internal 4-bit value for 7-segment decoder
    signal s_digit : std_logic_vector(4 - 1 downto 0);    -- Internal 4-bit value number for FSM_doorlock
    signal s_cnp   : std_logic_vector(3 - 1 downto 0);    -- Internal 3-bit position of the input value
    signal s_data0 : std_logic_vector(4 - 1 downto 0);    -- Internal 4-bit values for multiplexer
    signal s_data1 : std_logic_vector(4 - 1 downto 0);
    signal s_data2 : std_logic_vector(4 - 1 downto 0);
    signal s_data3 : std_logic_vector(4 - 1 downto 0);
    signal s_vel0  : std_logic_vector(4 - 1 downto 0);
    signal s_vel1  : std_logic_vector(4 - 1 downto 0);
    signal s_vel2  : std_logic_vector(4 - 1 downto 0);
    signal s_vel3  : std_logic_vector(4 - 1 downto 0);
    signal s_dist0 : std_logic_vector(4 - 1 downto 0);
    signal s_dist1 : std_logic_vector(4 - 1 downto 0);
    signal s_dist2 : std_logic_vector(4 - 1 downto 0);
    signal s_dist3 : std_logic_vector(4 - 1 downto 0);



    begin
    --------------------------------------------------------------------
    -- Instance of clock_enable entity that generates an enable pulse
    clk_en0 : entity work.clock_enable
        generic map(
            g_MAX => 4
        )
        port map(
            clk   => clk,
            reset => reset,
            ce_o  => s_en
        );

    --------------------------------------------------------------------
    -- Instance of cnt_up_down entity performs a 2-bit counter
    bin_cnt0 : entity work.cnt_up_down
        generic map(
            g_CNT_WIDTH => 2
        )
        port map(
            clk      => clk,
            reset    => reset,
            en_i     => s_en,
            cnt_up_i => '1',
            cnt_o    => s_cnt
        );

    --------------------------------------------------------------------
    -- Instance of hex_7seg entity performs a 7-segment display decoder
    hex2seg : entity work.hex_7seg
        port map(
            hex_i => s_hex,
            seg_o => seg_o
        );
        
    --------------------------------------------------------------------
    -- Instance of mux entity performs a 4-to-1 multiplexer
    mux_4to1 : entity work.mux
        port map(
            cnt_i   => s_cnt,
            data0_i => s_data0,
            data1_i => s_data1,
            data2_i => s_data2,
            data3_i => s_data3,
            seg_o   => s_hex,
            dig_o   => dig_o,
            dp_o    => dp_o
        );
        
    fsm : entity work.fsm
        port map(
            btn      => BTN,                       
            clk      => clk,                      
            vel0_i   => s_vel0,
            vel1_i   => s_vel1,
            vel2_i   => s_vel2,
            vel3_i   => s_vel3,
            dist0_i  => s_dist0,
            dist1_i  => s_dist1,
            dist2_i  => s_dist2,
            dist3_i  => s_dist3,
            data0_o  => s_data0,
            data1_o  => s_data1,
            data2_o  => s_data2,
            data3_o  => s_data3
        );
     distance : entity work.distance
        generic map(
        perimeter => 2
        )
        port map(
           clk       => clk,
           reset     => reset,
           sensor_i  => sensor_i,
           dist0_o   => s_dist0,
           dist1_o   => s_dist1,
           dist2_o   => s_dist2,
           dist3_o   => s_dist3
        );  
        
       velocity : entity work.velocity 
            generic map(                 
            perimeter => 2               
            )                            
            port map(                    
               clk      => clk,
               reset    => reset,
               sensor_i => sensor_i,
               vel0_o   => s_vel0,
               vel1_o   => s_vel1,
               vel2_o   => s_vel2,
               vel3_o   => s_vel3  
            );                                      
 end Behavioral;

```
